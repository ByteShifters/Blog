---
title: "Python Basics"
tags: ['Python']
date: 2024-09-11
draft: false
toc: true
unlisted: true
author: 'Ren'
authorlink: 'https://about.0x0060.dev/'
---


### **Python Setup**
Before you start coding in Python, you need to install Python on your computer and set up a development environment.

1. **Install Python**  
   Download and install the latest version of Python from the [official Python website](https://www.python.org/downloads/).

2. **Set Up a Development Environment**  
   Choose an editor or an integrated development environment (IDE) to write and run your Python code. Popular choices include:
   - **VS Code**: A lightweight code editor with rich Python support.
   - **PyCharm**: A feature-rich IDE specifically designed for Python development.
   - **Jupyter Notebook**: An interactive notebook, great for data analysis and scientific computing.

### **Basic Syntax and Data Types**

#### **Variables and Data Types**
In Python, variables are used to store data. Python has several built-in data types:

- **`int`**: Represents whole numbers.
  ```
  age = 25
  ```
- **`float`**: Represents decimal numbers.
  ```
  price = 19.99
  ```
- **`str`**: Represents a sequence of characters (a string).
  ```
  name = "Alice"
  ```
- **`bool`**: Represents a boolean value, either `True` or `False`.
  ```
  is_student = True
  ```

#### **Input/Output Operations**
Python allows you to take input from users and display output.

- **`input()`**: Used to take input from the user. Input is always read as a string.
  ```
  name = input("Enter your name: ")
  ```
- **`print()`**: Used to display output.
  ```
  print("Hello, World!")
  print("Your name is", name)
  ```

### **Operators**

#### **Arithmetic Operators**
These operators are used to perform basic mathematical operations:

- `+`: Addition
  ```
  sum = 10 + 5  # sum is 15
  ```
- `-`: Subtraction
  ```
  difference = 10 - 5  # difference is 5
  ```
- `*`: Multiplication
  ```
  product = 10 * 5  # product is 50
  ```
- `/`: Division (returns a float)
  ```
  quotient = 10 / 5  # quotient is 2.0
  ```
- `//`: Floor Division (returns an integer)
  ```
  quotient = 10 // 3  # quotient is 3
  ```
- `%`: Modulus (remainder)
  ```
  remainder = 10 % 3  # remainder is 1
  ```
- `**`: Exponentiation (power)
  ```
  power = 2 ** 3  # power is 8
  ```

#### **Comparison Operators**
These operators are used to compare values. They return `True` or `False`:

- `==`: Equal to
  ```
  result = 5 == 5  # result is True
  ```
- `!=`: Not equal to
  ```
  result = 5 != 3  # result is True
  ```
- `>`: Greater than
  ```
  result = 5 > 3  # result is True
  ```
- `<`: Less than
  ```
  result = 3 < 5  # result is True
  ```
- `>=`: Greater than or equal to
  ```
  result = 5 >= 5  # result is True
  ```
- `<=`: Less than or equal to
  ```
  result = 3 <= 5  # result is True
  ```

#### **Logical Operators**
These operators are used to combine conditional statements:

- `and`: Returns `True` if both statements are true
  ```
  result = (5 > 3) and (8 > 6)  # result is True
  ```
- `or`: Returns `True` if at least one statement is true
  ```
  result = (5 > 3) or (8 < 6)  # result is True
  ```
- `not`: Reverses the result, returns `False` if the result is true
  ```
  result = not(5 > 3)  # result is False
  ```

### **Control Structures**

#### **Conditional Statements**
Conditional statements allow you to execute certain code based on whether a condition is true or false.

- **`if` Statement**: Executes the block of code inside it if the condition is true.
  ```
  age = 18
  if age >= 18:
      print("You are an adult.")
  ```
- **`elif` Statement**: Allows you to check multiple expressions for `True` and execute a block of code as soon as one of the conditions is true.
  ```
  if age < 13:
      print("You are a child.")
  elif age < 18:
      print("You are a teenager.")
  else:
      print("You are an adult.")
  ```
