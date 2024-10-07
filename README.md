# attendance-management-system
#This application provides a basic interface for managing student attendance records stored in a MySQL database. It demonstrates fundamental concepts such as database connectivity, SQL operations (INSERT and UPDATE), event handling, and table population in a GUI environment using Java Swing.


package com.company;

public class Main {
    public static void main(String[] args) {
        new Attendance();
    }
}


//This is the entry point (main method) of the application.
//It creates an instance of the Attendance class, which initializes the GUI for managing attendance records.



package com.company;

import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.awt.event.*;
import java.sql.*;
import java.util.*;

public class Attendance {
    // GUI components
    private JTextField nameData;
    private JTextField totalClasses;
    private JTable table1;
    private JButton ADDRECORDButton;
    private JButton UPDATERECORDButton;
    private JPanel resultPanel;
    private JComboBox subject;
    private JTextField attendance;
    private JFrame attendF = new JFrame();
    public Attendance() {
        // Setting up JFrame and displaying the main panel
        attendF.setContentPane(resultPanel);
        attendF.pack();
        attendF.setLocationRelativeTo(null);
        attendF.setVisible(true);
        // Initialize table data
        tableData();
        // Action listener for ADDRECORDButton
        ADDRECORDButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                // Inserting a new record into the database
                if (nameData.getText().equals("") || attendance.getText().equals("")) {
                    JOptionPane.showMessageDialog(null, "Please Fill NAME and Total Classes Fields to add Record.");
                } else {
                    try {
                        String sql = "insert into attendance" + "(NAME,SUBJECT,TOTAL_CLASSES,CLASSES_ATTENDED,TOTAL_ATTENDANCE)" + "values (?,?,?,?,?)";
                        Class.forName("com.mysql.cj.jdbc.Driver");
                        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/intern", "root", "root");
                        PreparedStatement statement = connection.prepareStatement(sql);
                        double attend = (Double.parseDouble(attendance.getText()) / Double.parseDouble(totalClasses.getText())) * 100.0;
                        statement.setString(1, nameData.getText());
                        statement.setString(2, "" + subject.getSelectedItem());
                        statement.setString(3, totalClasses.getText());
                        statement.setString(4, attendance.getText());
                        statement.setString(5, String.format("%.2f", attend) + "%");
                        statement.executeUpdate();
                        JOptionPane.showMessageDialog(null, "STUDENT ADDED SUCCESSFULLY");
                        attendance.setText("");
                    } catch (Exception ex) {
                        JOptionPane.showMessageDialog(null, ex.getMessage());
                    }
                    tableData(); // Refresh the table after adding a record
                }
            }
        });
        // Action listener for UPDATERECORDButton
        UPDATERECORDButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                // Updating an existing record in the database
                String sql = "";
                double attend = (Double.parseDouble(attendance.getText()) / Double.parseDouble(totalClasses.getText())) * 100.0;
                String attend1 = String.format("%.2f", attend) + "%";
                try {
                    if (subject.getSelectedIndex() == 0) {
                        sql = "UPDATE attendance " + "SET CLASSES_ATTENDED= '" + attendance.getText() + "' ,TOTAL_ATTENDANCE= '" + attend1 + "' " + " WHERE NAME= '" + nameData.getText() + "' AND SUBJECT='PHYSICS'";
                    } else if (subject.getSelectedIndex() == 1) {
                        sql = "UPDATE attendance " + "SET CLASSES_ATTENDED= '" + attendance.getText() + "' ,TOTAL_ATTENDANCE= '" + attend1 + "' " + " WHERE NAME= '" + nameData.getText() + "' AND SUBJECT='CHEMISTRY'";
                    } else {
                        sql = "UPDATE attendance " + "SET CLASSES_ATTENDED= '" + attendance.getText() + "' ,TOTAL_ATTENDANCE= '" + attend1 + "' " + " WHERE NAME= '" + nameData.getText() + "' AND SUBJECT='MATHS'";
                    }
                    Class.forName("com.mysql.cj.jdbc.Driver");
                    Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/intern", "root", "root");
                    PreparedStatement statement = connection.prepareStatement(sql);
                    statement.executeUpdate();
                    JOptionPane.showMessageDialog(null, "Attendance Updated successfully");
                } catch (Exception e2) {
                    System.out.println(e2);
                }
                tableData(); // Refresh the table after updating a record
            }
        });
        // Mouse listener for table1 (JTable)
        table1.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseClicked(MouseEvent e) {
                // Populate text fields when a row in the table is clicked
                DefaultTableModel dm = (DefaultTableModel) table1.getModel();
                int rowIndex = table1.getSelectedRow();
                nameData.setText(dm.getValueAt(rowIndex, 0).toString());
                attendance.setText(dm.getValueAt(rowIndex, 3).toString());
                totalClasses.setText(dm.getValueAt(rowIndex, 2).toString());
            }
        });
    }
    // Method to populate the JTable with data from the database
    public void tableData() {
        try {
            String query = "Select * from attendance";
            Class.forName("com.mysql.cj.jdbc.Driver");
            Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/intern", "root", "root");
            Statement statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery(query);
            table1.setModel(buildTableModel(resultSet)); // Set the table model with data
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(null, ex.getMessage());
        }
    }
    // Utility method to convert ResultSet to DefaultTableModel
    public static DefaultTableModel buildTableModel(ResultSet rs) throws SQLException {
        ResultSetMetaData metaData = rs.getMetaData();
        // Column names
        Vector<String> columnNames = new Vector<>();
        int columnCount = metaData.getColumnCount();
        for (int column = 1; column <= columnCount; column++) {
            columnNames.add(metaData.getColumnName(column));
        }
        // Data of the table
        Vector<Vector<Object>> data = new Vector<>();
        while (rs.next()) {
            Vector<Object> vector = new Vector<>();
            for (int columnIndex = 1; columnIndex <= columnCount; columnIndex++) {
                vector.add(rs.getObject(columnIndex));
            }
            data.add(vector);
        }
        return new DefaultTableModel(data, columnNames);
    }
}



Explanation:
GUI Components: The Attendance class defines various Swing components (JTextField, JTable, JButton, JComboBox, JPanel, JFrame) for managing attendance records.

Database Connection: The application connects to a MySQL database (intern) hosted locally (localhost:3306) with username root and password root.

Functionality:

Adding Records: When the ADDRECORDButton is clicked, it inserts a new attendance record into the attendance table in the database. It calculates attendance percentage and updates the GUI accordingly.

Updating Records: When the UPDATERECORDButton is clicked, it updates an existing attendance record in the database based on the selected subject (PHYSICS, CHEMISTRY, MATHS).

Displaying Records: The tableData() method fetches all records from the attendance table and populates the JTable (table1) using the buildTableModel() method, which converts a ResultSet to a DefaultTableModel.

Selecting Records: Clicking on a row in table1 populates the text fields (nameData, attendance, totalClasses) with corresponding data for editing or viewing.

