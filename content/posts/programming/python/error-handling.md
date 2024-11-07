---
title: "Error Handling and Exceptions"
tags: ['Python']
date: 2024-09-11
draft: false
toc: true
unlisted: true
author: 'Ren'
authorlink: 'https://about.0x0060.dev/'
---

### **Understanding Exceptions**
Exceptions are errors that occur during the execution of a program. Understanding common exceptions can help you handle errors more effectively.

- **Common Exceptions**:
  - **`IndexError`**: Raised when trying to access an element from a list using an index that is out of range.
    ```
    my_list = [1, 2, 3]
    item = my_list[5]  # Raises IndexError
    ```

  - **`KeyError`**: Raised when trying to access a key in a dictionary that does not exist.
    ```
    my_dict = {"name": "Alice"}
    value = my_dict["age"]  # Raises KeyError
    ```

  - **`TypeError`**: Raised when an operation or function is applied to an object of inappropriate type.
    ```
    result = "hello" + 5  # Raises TypeError
    ```

  - **`ValueError`**: Raised when a function receives an argument of the right type but inappropriate value.
    ```
    number = int("abc")  # Raises ValueError
    ```

  - **`FileNotFoundError`**: Raised when trying to open a file that does not exist.
    ```
    file = open("nonexistent_file.txt")  # Raises FileNotFoundError
    ```

### **Handling Exceptions**
You can handle exceptions using `try` and `except` blocks. This allows your program to continue executing even if an error occurs.

- **Using `try`, `except`, `finally`, `else`**:

  - **`try`**: Block of code where you anticipate an exception might occur.
  - **`except`**: Block of code that will execute if an exception occurs in the `try` block.
  - **`else`**: Block of code that will execute if no exception occurs in the `try` block.
  - **`finally`**: Block of code that will execute regardless of whether an exception occurs or not.

  **Example**:
  ```
  try:
      file = open("example.txt", "r")
      content = file.read()
  except FileNotFoundError:
      print("File not found.")
  except IOError:
      print("An I/O error occurred.")
  else:
      print("File read successfully.")
  finally:
      file.close()
      print("File closed.")
  ```

- **Raising Exceptions**:
  You can raise exceptions deliberately using the `raise` keyword. This is useful for creating custom error messages or controlling program flow.

  **Example**:
  ```
  def divide(x, y):
      if y == 0:
          raise ValueError("Cannot divide by zero!")
      return x / y

  try:
      result = divide(10, 0)
  except ValueError as e:
      print(e)  # Prints "Cannot divide by zero!"
  ```

### **Custom Exceptions**
You can create your own exception classes by subclassing the built-in `Exception` class. This is useful for defining exceptions that are specific to your application.

- **Creating Custom Exception Classes**:

  **Example**:
  ```
  class CustomError(Exception):
      """Base class for other exceptions"""
      pass

  class SpecificError(CustomError):
      """Raised for specific conditions"""
      def __init__(self, message):
          self.message = message
          super().__init__(self.message)

  def test(value):
      if value < 0:
          raise SpecificError("Negative value error!")
      return value

  try:
      test(-1)
  except SpecificError as e:
      print(e)  # Prints "Negative value error!"
  ```
