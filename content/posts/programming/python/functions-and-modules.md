---
title: 'Functions and Modules'
tags: ['Python']
date: 2024-09-11
draft: false
toc: true
unlisted: true
---

### **Functions**
Functions are blocks of reusable code that perform a specific task.

- **Defining Functions**  
  You can define a function using the `def` keyword, followed by the function name and parentheses `()`.
  ```
  def greet(name):
      return f"Hello, {name}!"
  ```
  ```
  greeting = greet("Alice")  # greeting is "Hello, Alice!"
  ```

- **Arguments and Return Values**  
  Functions can take arguments (inputs) and return a value.
  ```
  def add(a, b):
      return a + b

  result = add(3, 5)  # result is 8
  ```

- **Default Arguments**  
  You can assign default values to function arguments, which will be used if no value is provided.
  ```
  def greet(name, message="Hello"):
      return f"{message}, {name}!"

  greeting = greet("Alice")  # greeting is "Hello, Alice!"
  greeting_custom = greet("Bob", "Hi")  # greeting_custom is "Hi, Bob!"
  ```

- **Keyword Arguments**  
  You can pass arguments by name, which makes the function call more readable.
  ```
  def greet(name, message):
      return f"{message}, {name}!"

  greeting = greet(message="Good morning", name="Alice")  # greeting is "Good morning, Alice!"
  ```

- **Variable-Length Arguments**  
  You can use `*args` for variable-length positional arguments and `**kwargs` for variable-length keyword arguments.
  ```
  def add(*args):
      return sum(args)

  result = add(1, 2, 3, 4)  # result is 10
  ```
  ```
  def describe_person(name, **kwargs):
      description = f"Name: {name}\n"
      for key, value in kwargs.items():
          description += f"{key.capitalize()}: {value}\n"
      return description

  person_description = describe_person("Alice", age=30, city="New York")
  # person_description is:
  # "Name: Alice\nAge: 30\nCity: New York\n"
  ```

- **Lambda Functions**  
  Lambda functions are small anonymous functions defined with the `lambda` keyword. They can have any number of arguments but only one expression.
  ```
  square = lambda x: x ** 2
  result = square(5)  # result is 25
  ```
  
  ```
  add = lambda a, b: a + b
  result = add(3, 5)  # result is 8
  ```

### **Modules**
Modules are files containing Python code (functions, classes, variables) that can be imported and used in other Python programs.

- **Importing Standard Libraries**  
  Python has a rich standard library that you can import and use in your programs.
  ```
  import math
  import random
  import datetime
  ```
  
  **Examples**:
  - **`math`**: Provides mathematical functions.
    ```
    result = math.sqrt(16)  # result is 4.0
    ```
  
  - **`random`**: Provides functions for generating random numbers.
    ```
    random_number = random.randint(1, 10)  # random_number is a random integer between 1 and 10
    ```
  
  - **`datetime`**: Provides classes for manipulating dates and times.
    ```
    current_time = datetime.datetime.now()  # current_time is the current date and time
    ```

- **Creating and Using Custom Modules**  
  You can create your own modules by writing Python code in a `.py` file and then importing it into other programs.
  
  **Example**: Create a file named `mymodule.py` with the following content:
  ```
  # mymodule.py

  def greet(name):
      return f"Hello, {name}!"
  ```
  
  You can then import and use this module in another file:
  ```
  import mymodule

  greeting = mymodule.greet("Alice")  # greeting is "Hello, Alice!"
  ```

- **Exploring the Python Package Index (PyPI)**  
  PyPI is a repository of software for the Python programming language. You can install packages from PyPI using `pip`.

  **Example**: Install the `requests` library using pip:
  ```sh
  pip install requests
  ```
  
  After installing, you can use the package in your code:
  ```
  import requests

  response = requests.get("https://api.github.com")
  data = response.json()
  ```
