# Commands used to host MySql Server on AWS EC2 Instance

## Step 1: Update the system

sudo apt update

## Step 2: Install MySql

sudo apt install mysql-server

## Step 3: Check the Status of MySql (Active or Inactive)

sudo systemctl status mysql

## Step 4: Login to MySql as a root

sudo mysql

## Step 5: Update the password for the MySql Server

ALTER USER 'root'@'localhost' IDENTIFIED BY 'place-your-password-here';  -> This works for MySQL
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'place-your-password-here';  ->  This works for MariaDB

FLUSH PRIVILEGES;

## Step 6: Test the MySql server if it is working by running sample sql queries

### Run sample queries manually

CREATE DATABASE mysql_test;

USE mysql_test;

CREATE TABLE table1 (id INT, name VARCHAR(45));

INSERT INTO table1 VALUES(1, 'Virat'), (2, 'Sachin'), (3, 'Dhoni'), (4, 'ABD');

SELECT * FROM table1;


### Create database from .Sql file by cloning repository

From Step 4 or use 'quit;' to quit sql query

gh repo clone Mikehade/Data-Mart

cd Data-Mart

sudo mysql

SOURCE datamart.sql;

SELECT * FROM customer;

SELECT customer.first_name AS 'first_name', customer.last_name AS 'last_name', dependents.spouse AS 'Spouse Status', dependents.children AS 'children' FROM customer JOIN dependents ON customer.dependents_id = dependents.id ORDER BY first_name;

OR

c.first_name AS 'first_name', c.last_name AS 'last_name', d.spouse AS 'Spouse Status', d.children AS 'children' FROM customer c JOIN dependents d ON c.dependents_id = d.id ORDER BY first_name;


## Connect server to local DBMS GUI

Go to securiy settings in the EC2 Instance Console and add HTTP connection for MySQL/Aurora (post:3306)

### Connect server to TablePlus



### Connect server to MySQL Workbench



