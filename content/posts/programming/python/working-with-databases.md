---
title: "Working with Databases"
tags: ['Python']
date: 2024-09-11
draft: false
toc: true
unlisted: true
---

### **SQL Basics**

- **Understanding SQL and Databases**  
  SQL (Structured Query Language) is used to interact with relational databases. A database is a collection of structured data that can be easily accessed, managed, and updated.

- **Basic SQL Commands**  
  Here are some fundamental SQL commands for interacting with a database:

  - **`SELECT`**: Retrieves data from a database.
    **Example**:
    ```
    SELECT * FROM users;
    ```

  - **`INSERT`**: Adds new rows to a table.
    **Example**:
    ```
    INSERT INTO users (name, age) VALUES ('Alice', 30);
    ```

  - **`UPDATE`**: Modifies existing data within a table.
    **Example**:
    ```
    UPDATE users SET age = 31 WHERE name = 'Alice';
    ```

  - **`DELETE`**: Removes rows from a table.
    **Example**:
    ```
    DELETE FROM users WHERE name = 'Alice';
    ```

### **Connecting Python to Databases**

- **Using `sqlite3`**  
  SQLite is a lightweight, disk-based database that doesn't require a separate server process. Python's `sqlite3` module allows you to work with SQLite databases.

  **Example**:
  ```
  import sqlite3

  # Connect to (or create) a SQLite database
  conn = sqlite3.connect('example.db')
  cursor = conn.cursor()

  # Create a table
  cursor.execute('''CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT, age INTEGER)''')

  # Insert a row
  cursor.execute("INSERT INTO users (name, age) VALUES ('Alice', 30)")

  # Commit and close
  conn.commit()
  conn.close()

  # Query data
  conn = sqlite3.connect('example.db')
  cursor = conn.cursor()
  cursor.execute("SELECT * FROM users")
  rows = cursor.fetchall()
  print(rows)  # Output: [(1, 'Alice', 30)]
  conn.close()
  ```

- **Using `MySQL`**  
  To connect to a MySQL database, you can use the `mysql-connector-python` package.

  **Example**:
  ```
  import mysql.connector

  # Connect to a MySQL database
  conn = mysql.connector.connect(
      host="localhost",
      user="yourusername",
      password="yourpassword",
      database="testdb"
  )
  cursor = conn.cursor()

  # Create a table
  cursor.execute("CREATE TABLE users (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(255), age INT)")

  # Insert a row
  cursor.execute("INSERT INTO users (name, age) VALUES ('Alice', 30)")

  # Commit and close
  conn.commit()
  conn.close()

  # Query data
  conn = mysql.connector.connect(
      host="localhost",
      user="yourusername",
      password="yourpassword",
      database="testdb"
  )
  cursor = conn.cursor()
  cursor.execute("SELECT * FROM users")
  rows = cursor.fetchall()
  print(rows)  # Output: [(1, 'Alice', 30)]
  conn.close()
  ```

- **Using `PostgreSQL`**  
  For PostgreSQL, the `psycopg2` library is commonly used.

  **Example**:
  ```
  import psycopg2

  # Connect to a PostgreSQL database
  conn = psycopg2.connect(
      dbname="testdb",
      user="yourusername",
      password="yourpassword",
      host="localhost"
  )
  cursor = conn.cursor()

  # Create a table
  cursor.execute("CREATE TABLE users (id SERIAL PRIMARY KEY, name VARCHAR(255), age INTEGER)")

  # Insert a row
  cursor.execute("INSERT INTO users (name, age) VALUES ('Alice', 30)")

  # Commit and close
  conn.commit()
  conn.close()

  # Query data
  conn = psycopg2.connect(
      dbname="testdb",
      user="yourusername",
      password="yourpassword",
      host="localhost"
  )
  cursor = conn.cursor()
  cursor.execute("SELECT * FROM users")
  rows = cursor.fetchall()
  print(rows)  # Output: [(1, 'Alice', 30)]
  conn.close()
  ```

### **ORM Basics with SQLAlchemy**

SQLAlchemy is an ORM (Object-Relational Mapping) library that allows you to work with databases using Python objects.

- **Setting Up SQLAlchemy**  
  Install SQLAlchemy with pip:
  ```bash
  pip install sqlalchemy
  ```

  **Example**:
  ```
  from sqlalchemy import create_engine, Column, Integer, String
  from sqlalchemy.ext.declarative import declarative_base
  from sqlalchemy.orm import sessionmaker

  # Setup database connection
  engine = create_engine('sqlite:///example.db')
  Base = declarative_base()

  # Define a User model
  class User(Base):
      __tablename__ = 'users'
      id = Column(Integer, primary_key=True)
      name = Column(String)
      age = Column(Integer)

  # Create tables
  Base.metadata.create_all(engine)

  # Create a new session
  Session = sessionmaker(bind=engine)
  session = Session()

  # Insert a new user
  new_user = User(name='Alice', age=30)
  session.add(new_user)
  session.commit()

  # Query data
  users = session.query(User).all()
  for user in users:
      print(user.id, user.name, user.age)  # Output: 1 Alice 30

  session.close()
  ```

In this example, SQLAlchemy is used to define a `User` class, create a table, and perform basic operations such as inserting and querying data.
