import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.io.*;

public class SmartExpenseTracker extends JFrame {

    private JTextField dateField, amountField;
    private JComboBox<String> categoryBox, searchBox;
    private JTable table;
    private DefaultTableModel model;
    private JLabel totalLabel;

    private double totalAmount = 0;
    private boolean darkMode = false;

    public SmartExpenseTracker() {

        setTitle("Smart Expense Tracker");
        setSize(850, 550);
        setLocationRelativeTo(null);
        setDefaultCloseOperation(EXIT_ON_CLOSE);

        // ===== INPUT PANEL =====
        JPanel inputPanel = new JPanel();

        dateField = new JTextField(8);
        amountField = new JTextField(8);

        String[] categories = {"Food", "Travel", "Shopping", "Bills", "Other"};
        categoryBox = new JComboBox<>(categories);

        JButton addButton = new JButton("Add Expense");

        inputPanel.add(new JLabel("Date (dd-mm-yyyy):"));
        inputPanel.add(dateField);
        inputPanel.add(new JLabel("Amount:"));
        inputPanel.add(amountField);
        inputPanel.add(new JLabel("Category:"));
        inputPanel.add(categoryBox);
        inputPanel.add(addButton);

        // ===== TABLE =====
        model = new DefaultTableModel();
        model.addColumn("Date");
        model.addColumn("Amount");
        model.addColumn("Category");

        table = new JTable(model);
        JScrollPane scrollPane = new JScrollPane(table);

        // ===== BOTTOM PANEL =====
        JPanel bottomPanel = new JPanel();

        totalLabel = new JLabel("Total: 0");

        JButton deleteButton = new JButton("Delete");
        JButton monthlyButton = new JButton("Monthly Report");
        JButton exportButton = new JButton("Export CSV");
        JButton darkButton = new JButton("Toggle Dark Mode");

        searchBox = new JComboBox<>(new String[]{"All","Food","Travel","Shopping","Bills","Other"});
        JButton searchButton = new JButton("Filter");

        bottomPanel.add(totalLabel);
        bottomPanel.add(deleteButton);
        bottomPanel.add(new JLabel("Filter:"));
        bottomPanel.add(searchBox);
        bottomPanel.add(searchButton);
        bottomPanel.add(monthlyButton);
        bottomPanel.add(exportButton);
        bottomPanel.add(darkButton);

        add(inputPanel, BorderLayout.NORTH);
        add(scrollPane, BorderLayout.CENTER);
        add(bottomPanel, BorderLayout.SOUTH);

        // ===== ACTIONS =====
        addButton.addActionListener(e -> addExpense());
        deleteButton.addActionListener(e -> deleteExpense());
        searchButton.addActionListener(e -> filterExpenses());
        monthlyButton.addActionListener(e -> showMonthlyReport());
        exportButton.addActionListener(e -> exportCSV());
        darkButton.addActionListener(e -> toggleDarkMode());

        loadFromFile();
        setVisible(true);
    }

    // ===== ADD EXPENSE =====
    private void addExpense() {
        try {
            String date = dateField.getText();
            double amount = Double.parseDouble(amountField.getText());
            String category = (String) categoryBox.getSelectedItem();

            model.addRow(new Object[]{date, amount, category});
            totalAmount += amount;
            totalLabel.setText("Total: " + totalAmount);

            saveToFile();

            dateField.setText("");
            amountField.setText("");

        } catch (Exception ex) {
            JOptionPane.showMessageDialog(this, "Enter valid data!");
        }
    }

    // ===== DELETE =====
    private void deleteExpense() {
        int row = table.getSelectedRow();
        if (row != -1) {
            double amount = (double) model.getValueAt(row, 1);
            totalAmount -= amount;
            model.removeRow(row);
            totalLabel.setText("Total: " + totalAmount);
            saveToFile();
        }
    }

    // ===== FILTER =====
    private void filterExpenses() {
        String selected = (String) searchBox.getSelectedItem();

        model.setRowCount(0);
        totalAmount = 0;

        File file = new File("expenses.txt");
        if (!file.exists()) return;

        try (BufferedReader reader = new BufferedReader(new FileReader(file))) {
            String line;
            while ((line = reader.readLine()) != null) {

                String[] data = line.split(",");
                String date = data[0];
                double amount = Double.parseDouble(data[1]);
                String category = data[2];

                if (selected.equals("All") || category.equals(selected)) {
                    model.addRow(new Object[]{date, amount, category});
                    totalAmount += amount;
                }
            }
            totalLabel.setText("Total: " + totalAmount);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // ===== MONTHLY REPORT =====
    private void showMonthlyReport() {

        String month = JOptionPane.showInputDialog(this,"Enter month (mm)");

        if (month == null) return;

        double monthlyTotal = 0;

        for(int i=0;i<model.getRowCount();i++){
            String date = (String) model.getValueAt(i,0);

            if(date.length() >= 5 && date.split("-")[1].equals(month)){
                monthlyTotal += (double) model.getValueAt(i,1);
            }
        }

        JOptionPane.showMessageDialog(this,
                "Total Expense for Month "+month+" : "+monthlyTotal);
    }

    // ===== EXPORT CSV =====
    private void exportCSV(){

        try(PrintWriter writer = new PrintWriter("ExpenseReport.csv")){

            writer.println("Date,Amount,Category");

            for(int i=0;i<model.getRowCount();i++){
                writer.println(model.getValueAt(i,0)+","+
                        model.getValueAt(i,1)+","+
                        model.getValueAt(i,2));
            }

            JOptionPane.showMessageDialog(this,"Exported Successfully!");

        }catch(Exception e){
            e.printStackTrace();
        }
    }

    // ===== SAVE FILE =====
    private void saveToFile() {

        try (PrintWriter writer = new PrintWriter("expenses.txt")) {

            for (int i = 0; i < model.getRowCount(); i++) {
                writer.println(model.getValueAt(i,0)+","+
                        model.getValueAt(i,1)+","+
                        model.getValueAt(i,2));
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // ===== LOAD FILE =====
    private void loadFromFile() {

        File file = new File("expenses.txt");
        if (!file.exists()) return;

        try (BufferedReader reader = new BufferedReader(new FileReader(file))) {

            String line;
            while ((line = reader.readLine()) != null) {

                String[] data = line.split(",");
                String date = data[0];
                double amount = Double.parseDouble(data[1]);
                String category = data[2];

                model.addRow(new Object[]{date, amount, category});
                totalAmount += amount;
            }

            totalLabel.setText("Total: " + totalAmount);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // ===== DARK MODE =====
    private void toggleDarkMode() {

        darkMode = !darkMode;

        if (darkMode) {
            getContentPane().setBackground(Color.DARK_GRAY);
            table.setBackground(Color.GRAY);
            table.setForeground(Color.WHITE);
        } else {
            getContentPane().setBackground(null);
            table.setBackground(Color.WHITE);
            table.setForeground(Color.BLACK);
        }
    }

    public static void main(String[] args) {
        new SmartExpenseTracker();
    }
}
