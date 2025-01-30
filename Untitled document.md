# Spring MVC CRUD Application with MySQL

This repository contains the code for creating a simple **Spring MVC CRUD** (Create, Read, Update, Delete) application using **MySQL** as the database.

### Table of Contents

- [Introduction](#introduction)
- [Pre-requisites](#pre-requisites)
- [Step-by-Step Setup](#step-by-step-setup)
  - [Step 1: Install Necessary Software](#step-1-install-necessary-software)
  - [Step 2: Set Up Your Project in Eclipse](#step-2-set-up-your-project-in-eclipse)
  - [Step 3: Set Up MySQL Database](#step-3-set-up-mysql-database)
  - [Step 4: Configure Database in Spring Project](#step-4-configure-database-in-spring-project)
  - [Step 5: Add Dependencies (Libraries)](#step-5-add-dependencies-libraries)
  - [Step 6: Create a Model Class (Item.java)](#step-6-create-a-model-class-itemjava)
  - [Step 7: Create a Repository Interface](#step-7-create-a-repository-interface)
  - [Step 8: Create a Service Layer](#step-8-create-a-service-layer)
  - [Step 9: Create a Controller Class](#step-9-create-a-controller-class)
  - [Step 10: Create Views (HTML Files)](#step-10-create-views-html-files)
  - [Step 11: Run the Application](#step-11-run-the-application)
- [Conclusion](#conclusion)

## Introduction

This tutorial will guide you through the steps to create a Spring MVC CRUD application with a MySQL database. You'll be able to create, read, update, and delete items from a list, and store them in a MySQL database.

## Pre-requisites

Before you start, make sure you have the following installed:

1. **Java Development Kit (JDK)**
2. **Eclipse IDE**
3. **MySQL Database**
4. **Spring Tools for Eclipse Plugin**

## Step-by-Step Setup

### Step 1: Install Necessary Software

You need to install the following tools on your computer:

1. **Java**: Download and install Java from [Oracle's website](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html).
2. **Eclipse IDE**: Download from [Eclipse's website](https://www.eclipse.org/downloads/).
3. **MySQL Database**: Download from [MySQL's website](https://dev.mysql.com/downloads/).
4. **Spring Tools for Eclipse**: Install this plugin via **Eclipse Marketplace** to make working with Spring easier.

### Step 2: Set Up Your Project in Eclipse

1. Open **Eclipse**.
2. Click on **File > New > Project > Spring > Spring Template Project**.
3. Choose **Spring MVC Project** and click **Next**.
4. Name your project (e.g., `SpringCRUDApp`) and click **Finish**.

### Step 3: Set Up MySQL Database

1. Open **MySQL Workbench** (or any MySQL tool you use).
2. Run the following commands to create a new database and a table to store your items:
   ```sql
   CREATE DATABASE spring_crud_db;
   CREATE TABLE items (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(100) NOT NULL,
       description VARCHAR(255) NOT NULL
   );

### Step 4: Configure Database in Spring Project
In your project, navigate to src/main/resources.

Open (or create) the application.properties file and add the following database configuration:
```application.properties
spring.datasource.url=jdbc:mysql://localhost:3306/spring_crud_db
spring.datasource.username=root
spring.datasource.password=rootpassword
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.hibernate.ddl-auto=update
spring.jpa.database-platform=org.hibernate.dialect.MySQL5InnoDBDialect
Replace rootpassword with your actual MySQL password.
```
### Step 5: Add Dependencies (Libraries)
Add the necessary libraries for MySQL and Spring MVC in the pom.xml file:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
### Step 6: Create a Model Class (Item.java)
This class represents an "item" that you want to store in the database. Create Item.java in the com.example.demo.model package:

```java
package com.example.demo.model;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Item {
   
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String description;
   
    // Getters and Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }
}

```
### Step 7: Create a Repository Interface
This interface allows interaction with the database for CRUD operations. Create ItemRepository.java in com.example.demo.repository:
```java
package com.example.demo.repository;

import com.example.demo.model.Item;
import org.springframework.data.repository.CrudRepository;

public interface ItemRepository extends CrudRepository<Item, Long> {
    // Custom methods can be added here if needed
}
```
### Step 8: Create a Service Layer
Create the service layer to handle the business logic for CRUD operations. Create ItemService.java in com.example.demo.service:

```java
package com.example.demo.service;

import com.example.demo.model.Item;
import com.example.demo.repository.ItemRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class ItemService {
   
    @Autowired
    private ItemRepository itemRepository;

    // Save Item
    public Item saveItem(Item item) {
        return itemRepository.save(item);
    }

    // Get All Items
    public Iterable<Item> getAllItems() {
        return itemRepository.findAll();
    }

    // Get Item by ID
    public Item getItemById(Long id) {
        return itemRepository.findById(id).orElse(null);
    }

    // Delete Item by ID
    public void deleteItem(Long id) {
        itemRepository.deleteById(id);
    }
}

```
### Step 9: Create a Controller Class
The controller handles HTTP requests and serves pages. Create ItemController.java in com.example.demo.controller:

```java
package com.example.demo.controller;

import com.example.demo.model.Item;
import com.example.demo.service.ItemService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

@Controller
@RequestMapping("/items")
public class ItemController {

    @Autowired
    private ItemService itemService;

    @GetMapping
    public String getAllItems(Model model) {
        model.addAttribute("items", itemService.getAllItems());
        return "items";
    }

    @GetMapping("/new")
    public String createItemForm(Model model) {
        model.addAttribute("item", new Item());
        return "create_item";
    }

    @PostMapping
    public String saveItem(@ModelAttribute Item item) {
        itemService.saveItem(item);
        return "redirect:/items";
    }

    @GetMapping("/edit/{id}")
    public String editItemForm(@PathVariable Long id, Model model) {
        Item item = itemService.getItemById(id);
        model.addAttribute("item", item);
        return "edit_item";
    }

    @PostMapping("/edit/{id}")
    public String updateItem(@PathVariable Long id, @ModelAttribute Item item) {
        item.setId(id);
        itemService.saveItem(item);
        return "redirect:/items";
    }

    @GetMapping("/delete/{id}")
    public String deleteItem(@PathVariable Long id) {
        itemService.deleteItem(id);
        return "redirect:/items";
    }
}

```
### Step 10: Create Views (HTML Files)
Create the necessary HTML files for the user interface. These files should be placed in src/main/resources/templates:

items.html - To display the list of items.
create_item.html - To create a new item.
edit_item.html - To edit an existing item.
Example of items.html:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Items List</title>
</head>
<body>
    <h1>Items</h1>
    <a href="/items/new">Create New Item</a>
    <table>
        <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Actions</th>
        </tr>
        <th:block th:each="item : ${items}">
            <tr>
                <td th:text="${item.name}"></td>
                <td th:text="${item.description}"></td>
                <td>
                    <a th:href="@{/items/edit/{id}(id=${item.id})}">Edit</a>
                    <a th:href="@{/items/delete/{id}(id=${item.id})}">Delete</a>
                </td>
            </tr>
        </th:block>
    </table>
</body>
</html>
```

### Step 11: Create Views (HTML Files)
Create the necessary HTML files for the user interface. These files should be placed in src/main/resources/templates:
CREATE.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Create Item</title>
</head>
<body>
    <h1>Create a New Item</h1>

    <form action="/items" method="POST">
        <!-- Name Field -->
        <label for="name">Item Name:</label>
        <input type="text" id="name" name="name" required>
        <br><br>

        <!-- Description Field -->
        <label for="description">Item Description:</label>
        <textarea id="description" name="description" required></textarea>
        <br><br>

        <!-- Submit Button -->
        <button type="submit">Create Item</button>
    </form>

    <br>
    <a href="/items">Back to Items List</a>
</body>
</html>

```
### Step 12: Create Views (HTML Files)
Create the necessary HTML files for the user interface. These files should be placed in src/main/resources/templates:
Edit.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Edit Item</title>
</head>
<body>
    <h1>Edit Item</h1>

    <!-- Form to Edit Item -->
    <form action="/items/edit/{{item.id}}" method="POST">
        <!-- Name Field -->
        <label for="name">Item Name:</label>
        <input type="text" id="name" name="name" value="{{item.name}}" required>
        <br><br>

        <!-- Description Field -->
        <label for="description">Item Description:</label>
        <textarea id="description" name="description" required>{{item.description}}</textarea>
        <br><br>

        <!-- Submit Button -->
        <button type="submit">Update Item</button>
    </form>

    <br>
    <a href="/items">Back to Items List</a>
</body>
</html>

```
### Step 13: Run the Application
1. Right-click on the project and select Run As > Spring Boot App.
2. Open your browser and navigate to http://localhost:8080/items.

## Conclusion
Now, you have a simple Spring MVC CRUD application that allows you to add, view, update, and delete items in a MySQL database.


