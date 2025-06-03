# User-and-Admin-Authentication-
This is my first project displaying work that I have learned during my first year and second year at TUT and Eduvos

//Database Setup
-- users table
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    role ENUM('admin', 'user') NOT NULL
);

-- sample data
INSERT INTO users (username, password, role) VALUES
('admin1', 'adminpass', 'admin'),
('user1', 'userpass', 'user');
//Project management Java Code Folder structure

ProjectManager/
├── DBConnection.java
├── User.java
└── LoginSystem.java

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

//DBConnection.java
public class DBConnection {
    private static final String URL = "jdbc:mysql://localhost:3306/your_database_name";
    private static final String USER = "your_db_user";
    private static final String PASSWORD = "your_db_password";

    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASSWORD);
    }
}

//user interface 
public class User {
    private int id;
    private String username;
    private String role;

    public User(int id, String username, String role) {
        this.id = id;
        this.username = username;
        this.role = role;
    }

    public String getRole() {
        return role;
    }

    public String getUsername() {
        return username;
    }
}


// Log in system interface
import java.sql.*;
import java.util.Scanner;

public class LoginSystem {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.print("Username: ");
        String username = scanner.nextLine();
        System.out.print("Password: ");
        String password = scanner.nextLine();

        User user = authenticateUser(username, password);
        if (user != null) {
            System.out.println("Welcome " + user.getUsername() + "!");
            if (user.getRole().equals("admin")) {
                System.out.println("You have admin access.");
                // Admin-specific features here
            } else {
                System.out.println("You have user access.");
                // User-specific features here
            }
        } else {
            System.out.println("Invalid login. Please try again.");
        }
        scanner.close();
    }

    public static User authenticateUser(String username, String password) {
        String query = "SELECT * FROM users WHERE username = ? AND password = ?";
        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(query)) {
            
            stmt.setString(1, username);
            stmt.setString(2, password);
            ResultSet rs = stmt.executeQuery();

            if (rs.next()) {
                return new User(
                    rs.getInt("id"),
                    rs.getString("username"),
                    rs.getString("role")
                );
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return null;
    }
}
