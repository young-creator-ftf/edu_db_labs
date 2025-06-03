# Реалізація доступу до бази даних
## 1. Модель `User.java`

```java
package model;

import java.util.UUID;

public class User {
    private UUID id;
    private String email;
    private String username;
    private String password;
    private String roleName;

    public User(UUID id, String email, String username, String password, String roleName) {
        this.id = id;
        this.email = email;
        this.username = username;
        this.password = password;
        this.roleName = roleName;
    }

    public UUID getId() { return id; }
    public String getEmail() { return email; }
    public String getUsername() { return username; }
    public String getPassword() { return password; }
    public String getRoleName() { return roleName; }

    public void setId(UUID id) { this.id = id; }
    public void setEmail(String email) { this.email = email; }
    public void setUsername(String username) { this.username = username; }
    public void setPassword(String password) { this.password = password; }
    public void setRoleName(String roleName) { this.roleName = roleName; }
}
```

**Пояснення:**  
Цей клас є відображенням таблиці `user` у вигляді Java-об’єкта. Всі поля повністю відповідають колонкам у БД. UUID використовується для зручної унікальної ідентифікації користувача.

---

## 2. Інтерфейс `UserDAO.java`

```java
package dao;

import model.User;
import java.util.List;
import java.util.UUID;

public interface UserDAO {
    void addUser(User user);
    User getUserById(UUID id);
    List<User> getAllUsers();
    void updateUser(User user);
    void deleteUser(UUID id);
}
```

**Пояснення:**  
Описано контракти (методи), які реалізуються у класі `UserDAOImpl`. Це базові CRUD-операції: створити, прочитати, оновити, видалити.

---

## 3. Реалізація `UserDAOImpl.java`

```java
package daoImpl;

import dao.UserDAO;
import model.User;
import util.DBConnection;

import java.sql.*;
import java.util.*;
import java.nio.ByteBuffer;

public class UserDAOImpl implements UserDAO {
    private final Connection conn = DBConnection.getConnection();

    public void addUser(User user) {
        String sql = "INSERT INTO user (id, email, username, password, role_name) VALUES (?, ?, ?, ?, ?)";
        try (PreparedStatement stmt = conn.prepareStatement(sql)) {
            stmt.setBytes(1, toBytes(user.getId()));
            stmt.setString(2, user.getEmail());
            stmt.setString(3, user.getUsername());
            stmt.setString(4, user.getPassword());
            stmt.setString(5, user.getRoleName());
            stmt.executeUpdate();
        } catch (SQLException e) { e.printStackTrace(); }
    }

    public User getUserById(UUID id) {
        String sql = "SELECT * FROM user WHERE id = ?";
        try (PreparedStatement stmt = conn.prepareStatement(sql)) {
            stmt.setBytes(1, toBytes(id));
            ResultSet rs = stmt.executeQuery();
            if (rs.next()) {
                return new User(
                    fromBytes(rs.getBytes("id")),
                    rs.getString("email"),
                    rs.getString("username"),
                    rs.getString("password"),
                    rs.getString("role_name")
                );
            }
        } catch (SQLException e) { e.printStackTrace(); }
        return null;
    }

    public List<User> getAllUsers() {
        List<User> users = new ArrayList<>();
        String sql = "SELECT * FROM user";
        try (Statement stmt = conn.createStatement()) {
            ResultSet rs = stmt.executeQuery(sql);
            while (rs.next()) {
                users.add(new User(
                    fromBytes(rs.getBytes("id")),
                    rs.getString("email"),
                    rs.getString("username"),
                    rs.getString("password"),
                    rs.getString("role_name")
                ));
            }
        } catch (SQLException e) { e.printStackTrace(); }
        return users;
    }

    public void updateUser(User user) {
        String sql = "UPDATE user SET email=?, username=?, password=?, role_name=? WHERE id=?";
        try (PreparedStatement stmt = conn.prepareStatement(sql)) {
            stmt.setString(1, user.getEmail());
            stmt.setString(2, user.getUsername());
            stmt.setString(3, user.getPassword());
            stmt.setString(4, user.getRoleName());
            stmt.setBytes(5, toBytes(user.getId()));
            stmt.executeUpdate();
        } catch (SQLException e) { e.printStackTrace(); }
    }

    public void deleteUser(UUID id) {
        String sql = "DELETE FROM user WHERE id=?";
        try (PreparedStatement stmt = conn.prepareStatement(sql)) {
            stmt.setBytes(1, toBytes(id));
            stmt.executeUpdate();
        } catch (SQLException e) { e.printStackTrace(); }
    }

    private byte[] toBytes(UUID uuid) {
        ByteBuffer bb = ByteBuffer.wrap(new byte[16]);
        bb.putLong(uuid.getMostSignificantBits());
        bb.putLong(uuid.getLeastSignificantBits());
        return bb.array();
    }

    private UUID fromBytes(byte[] bytes) {
        ByteBuffer bb = ByteBuffer.wrap(bytes);
        return new UUID(bb.getLong(), bb.getLong());
    }
}
```

**Пояснення:**  
Цей клас реалізує доступ до БД через JDBC. UUID конвертується в байтовий масив, бо в MySQL зберігається як BINARY(16). Запити безпечні завдяки використанню `PreparedStatement`.

---

## 4. Тест `TestUserDAO.java`

```java
package test;

import daoImpl.UserDAOImpl;
import model.User;

import java.util.UUID;

public class TestUserDAO {
    public static void main(String[] args) {
        UserDAOImpl dao = new UserDAOImpl();

        User u = new User(UUID.randomUUID(), "alice@example.com", "alice_dev", "alice123", "developer");
        dao.addUser(u);

        u.setUsername("alice_updated");
        dao.updateUser(u);

        System.out.println("Found: " + dao.getUserById(u.getId()).getUsername());

        dao.deleteUser(u.getId());
    }
}
```
# Клас DBConnection.java

Цей клас забезпечує єдиний спосіб створення з’єднання з базою даних MySQL через JDBC. Реалізовано патерн Singleton — одне з’єднання повторно використовується впродовж усього життєвого циклу програми.

## Код

```java
package com.example.util;

import java.sql.Connection;
import java.sql.DriverManager;

public class DBConnection {
    private static Connection connection;

    public static Connection getConnection() {
        if (connection == null) {
            try {
                connection = DriverManager.getConnection(
                    "jdbc:mysql://localhost:3306/studyuser", "root", "password"
                );
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        return connection;
    }
}
