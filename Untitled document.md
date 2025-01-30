Creating a Spring MVC CRUD (Create, Read, Update, Delete) application
with a MySQL database can seem complicated, but let\'s break it down
into easy-to-understand steps for a Grade 5 student. We will focus on
keeping things simple with lots of explanations. What is Spring MVC
CRUD? \* Spring MVC: This is a framework (a special set of tools) that
helps build web applications (websites). \* CRUD: This stands for
Create, Read, Update, and Delete, which are the basic operations that we
can do on data in a website. \* MySQL Database: This is where we store
our data (like information about users, products, etc.) in a table
format. In this tutorial, you will learn how to create a small website
where you can add, view, update, and delete items from a list (using
MySQL to store the data). \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

Step 1: Install Necessary Software Before you begin, you need to have a
few things installed on your computer: 1. Java: You need Java installed
to run Spring applications. You can download Java from Oracle\'s
website. 2. Eclipse IDE: Eclipse is a program that helps you write code.
You can download it from Eclipse\'s website. 3. MySQL Database: You will
need a MySQL database to store your data. You can download it from
MySQL\'s website. 4. Spring Tools for Eclipse: This is a plugin for
Eclipse that makes working with Spring projects easier. You can install
it from the Eclipse Marketplace. \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

Step 2: Set Up Your Project in Eclipse 1. Open Eclipse. 2. Click on File
\> New \> Project \> Spring \> Spring Template Project. 3. Choose Spring
MVC Project and click Next. 4. Name your project, for example,
SpringCRUDApp and click Finish. \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

Step 3: Set Up MySQL Database Now, let's set up a MySQL database where
we will store our items. 1. Open MySQL Workbench (or any MySQL tool you
use). Create a new database: CREATE DATABASE spring_crud_db; 2. Create a
table called items to store information about items: CREATE TABLE items
( id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(100) NOT NULL,
description VARCHAR(255) NOT NULL ); 3. \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

Step 4: Configure Database in Spring Project 1. Go to the
src/main/resources folder in your project.

Open application.properties (or create it if it doesn\'t exist) and add
the following configuration to connect your project to the MySQL
database:

spring.datasource.url=jdbc:mysql://localhost:3306/spring_crud_db
spring.datasource.username=root spring.datasource.password=rootpassword
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.hibernate.ddl-auto=update
spring.jpa.database-platform=org.hibernate.dialect.MySQL5InnoDBDialect
2. Make sure you change the username and password to your MySQL login
credentials.

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

Step 5: Add Dependencies (Libraries) You need to tell the project which
libraries to use for MySQL and Spring MVC. Add the following
dependencies in the pom.xml file (under the \<dependencies\> section):
\<dependency\> \<groupId\>org.springframework.boot\</groupId\>
\<artifactId\>spring-boot-starter-data-jpa\</artifactId\>
\</dependency\> \<dependency\> \<groupId\>mysql\</groupId\>
\<artifactId\>mysql-connector-java\</artifactId\> \</dependency\>
\<dependency\> \<groupId\>org.springframework.boot\</groupId\>
\<artifactId\>spring-boot-starter-web\</artifactId\> \</dependency\>

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

Step 6: Create a Model Class (Item.java) This class will represent an
\"item\" in our database. It\'s like a blueprint for our data.  1. In
your src/main/java folder, create a new Java class called Item inside
the package com.example.demo.model. Add this code: package
com.example.demo.model;

import javax.persistence.Entity; import
javax.persistence.GeneratedValue; import
javax.persistence.GenerationType; import javax.persistence.Id;

\@Entity public class Item {

\@Id \@GeneratedValue(strategy = GenerationType.IDENTITY) private Long
id; private String name; private String description;

// Getters and setters (this is just a way to get and set data) public
Long getId() { return id; }

public void setId(Long id) { this.id = id; }

public String getName() { return name; }

public void setName(String name) { this.name = name; }

public String getDescription() { return description; }

public void setDescription(String description) { this.description =
description; } } 2. This is the Item class that will represent the data
we want to store in the database. \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

Step 7: Create a Repository Interface This interface will help us
interact with the database (for saving, reading, updating, and deleting
items). Create a new interface called ItemRepository in the
com.example.demo.repository package: package
com.example.demo.repository;

import com.example.demo.model.Item; import
org.springframework.data.repository.CrudRepository;

public interface ItemRepository extends CrudRepository\<Item, Long\> {
// You can add more custom methods if needed }  1. This interface is
like a set of instructions for interacting with the database.
\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

Step 8: Create a Service Layer The service layer handles the logic of
your application (like saving data to the database). Create a class
called ItemService in the com.example.demo.service package. package
com.example.demo.service;

import com.example.demo.model.Item; import
com.example.demo.repository.ItemRepository; import
org.springframework.beans.factory.annotation.Autowired; import
org.springframework.stereotype.Service;

\@Service public class ItemService {

\@Autowired private ItemRepository itemRepository;

// Save Item public Item saveItem(Item item) { return
itemRepository.save(item); }

// Get All Items public Iterable\<Item\> getAllItems() { return
itemRepository.findAll(); }

// Get Item by ID public Item getItemById(Long id) { return
itemRepository.findById(id).orElse(null); }

// Delete Item by ID public void deleteItem(Long id) {
itemRepository.deleteById(id); } }

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

Step 9: Create a Controller Class The controller is the part of the code
that handles HTTP requests (what happens when someone visits a web
page). Create a new controller called ItemController in the
com.example.demo.controller package. package
com.example.demo.controller;

import com.example.demo.model.Item; import
com.example.demo.service.ItemService; import
org.springframework.beans.factory.annotation.Autowired; import
org.springframework.stereotype.Controller; import
org.springframework.ui.Model; import
org.springframework.web.bind.annotation.\*;

\@Controller \@RequestMapping(\"/items\") public class ItemController {

\@Autowired private ItemService itemService;

// Show all items \@GetMapping public String getAllItems(Model model) {
model.addAttribute(\"items\", itemService.getAllItems()); return
\"items\"; }

// Show form to add a new item \@GetMapping(\"/new\") public String
createItemForm(Model model) { model.addAttribute(\"item\", new Item());
return \"create_item\"; }

// Add a new item \@PostMapping public String saveItem(@ModelAttribute
Item item) { itemService.saveItem(item); return \"redirect:/items\"; }

// Show a form to edit an item \@GetMapping(\"/edit/{id}\") public
String editItemForm(@PathVariable Long id, Model model) { Item item =
itemService.getItemById(id); model.addAttribute(\"item\", item); return
\"edit_item\"; }

// Update an item \@PostMapping(\"/edit/{id}\") public String
updateItem(@PathVariable Long id, \@ModelAttribute Item item) {
item.setId(id); itemService.saveItem(item); return \"redirect:/items\";
}

// Delete an item \@GetMapping(\"/delete/{id}\") public String
deleteItem(@PathVariable Long id) { itemService.deleteItem(id); return
\"redirect:/items\"; } }

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

Step 10: Create Views (HTML Files) You need HTML files to show the user
interface (UI). Create the following files in the
src/main/resources/templates folder:  1. items.html -- To show the list
of items. 2. create_item.html -- To create a new item. 3. edit_item.html
-- To edit an existing item. For example, items.html might look like
this: \<!DOCTYPE html\> \<html\> \<head\> \<title\>Items List\</title\>
\</head\> \<body\> \<h1\>Items\</h1\> \<a href=\"/items/new\"\>Create
New Item\</a\> \<table\> \<tr\> \<th\>Name\</th\>
\<th\>Description\</th\> \<th\>Actions\</th\> \</tr\> \<th:block
th:each=\"item : \${items}\"\> \<tr\> \<td
th:text=\"\${item.name}\"\>\</td\> \<td
th:text=\"\${item.description}\"\>\</td\> \<td\> \<a
th:href=\"@{/items/edit/{id}(id=\${item.id})}\"\>Edit\</a\> \<a
th:href=\"@{/items/delete/{id}(id=\${item.id})}\"\>Delete\</a\> \</td\>
\</tr\> \</th:block\> \</table\> \</body\> \</html\>

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

Step 11: Run the Application  1. Right-click on the project in Eclipse
and click Run As \> Spring Boot App. 2. Open a web browser and go to
http://localhost:8080/items. \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

Conclusion: Now you have a simple Spring MVC CRUD application that
allows you to create, read, update, and delete items in a MySQL
database! You can use this as a base and expand on it with more
features.
