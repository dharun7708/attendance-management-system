# attendance-management-system
#This application provides a basic interface for managing student attendance records stored in a MySQL database. It demonstrates fundamental concepts such as database connectivity, SQL operations (INSERT and UPDATE), event handling, and table population in a GUI environment using Java Swing.


Explanation:
GUI Components: The Attendance class defines various Swing components (JTextField, JTable, JButton, JComboBox, JPanel, JFrame) for managing attendance records.

Database Connection: The application connects to a MySQL database (intern) hosted locally (localhost:3306) with username root and password root.

Functionality:

Adding Records: When the ADDRECORDButton is clicked, it inserts a new attendance record into the attendance table in the database. It calculates attendance percentage and updates the GUI accordingly.

Updating Records: When the UPDATERECORDButton is clicked, it updates an existing attendance record in the database based on the selected subject (PHYSICS, CHEMISTRY, MATHS).

Displaying Records: The tableData() method fetches all records from the attendance table and populates the JTable (table1) using the buildTableModel() method, which converts a ResultSet to a DefaultTableModel.

Selecting Records: Clicking on a row in table1 populates the text fields (nameData, attendance, totalClasses) with corresponding data for editing or viewing.

