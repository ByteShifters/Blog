---
title: 'Testing and Debugging'
tags: ['Python']
date: 2024-09-11
draft: false
toc: true
unlisted: true
author: 'Ren'
authorlink: 'https://about.0x0060.dev/'
---

### **Unit Testing**

- **Writing Test Cases Using `unittest` and `pytest`**  
  Unit testing involves writing test cases to verify that individual parts of your code (functions, methods, etc.) work as expected.

  - **`unittest`**: A built-in Python module for writing and running tests.
    **Example**:
    ```
    import unittest

    def add(a, b):
        return a + b

    class TestMathFunctions(unittest.TestCase):
        def test_add(self):
            self.assertEqual(add(1, 2), 3)
            self.assertEqual(add(-1, 1), 0)

    if __name__ == '__main__':
        unittest.main()
    ```

  - **`pytest`**: A third-party testing framework that is more flexible and has a simpler syntax than `unittest`.
    **Example**:
    ```
    def add(a, b):
        return a + b

    def test_add():
        assert add(1, 2) == 3
        assert add(-1, 1) == 0

    # To run the tests, use the command: pytest
    ```

- **Test-Driven Development (TDD)**  
  TDD is a software development approach where tests are written before the code itself. This ensures that the code is designed to meet the test requirements from the start.

  **TDD Workflow**:
  1. Write a failing test.
  2. Write the minimum amount of code to pass the test.
  3. Refactor the code while keeping the test green (passing).

- **Mocking and Patching**  
  Mocking allows you to replace parts of your system under test with mock objects and make assertions about how they have been used.

  - **Using `unittest.mock`**:
    **Example**:
    ```
    from unittest.mock import patch

    def get_data():
        # Imagine this function makes an API call
        return {"key": "value"}

    @patch('__main__.get_data', return_value={"key": "mocked_value"})
    def test_get_data(mock_get):
        result = get_data()
        assert result['key'] == 'mocked_value'

    # Running this with pytest or unittest will show that the function is successfully mocked.
    ```

### **Debugging**

- **Using `print()` for Debugging**  
  The simplest form of debugging is to insert `print()` statements in your code to check the flow of execution and the values of variables.

  **Example**:
  ```
  def complex_function(x):
      print(f"x at start: {x}")
      y = x * 2
      print(f"y after multiplication: {y}")
      z = y + 5
      print(f"z after addition: {z}")
      return z

  complex_function(10)
  ```
  Output:
  ```
  x at start: 10
  y after multiplication: 20
  z after addition: 25
  ```

- **Python Debugger: `pdb`**  
  `pdb` is Python’s built-in debugger. It allows you to set breakpoints, step through code, inspect variables, and evaluate expressions.

  **Example**:
  ```
  import pdb

  def complex_function(x):
      pdb.set_trace()  # Set a breakpoint
      y = x * 2
      z = y + 5
      return z

  complex_function(10)
  ```
  Running the above code will start an interactive debugging session at the point where `pdb.set_trace()` is called.

  Common `pdb` commands:
  - `n` (next): Continue execution to the next line within the same function.
  - `c` (continue): Continue execution until the next breakpoint.
  - `q` (quit): Exit the debugger and terminate the program.

- **Logging with the `logging` Module**  
  Logging is a more robust alternative to `print()` for tracking the execution of a program. The `logging` module provides a flexible framework for emitting log messages from Python programs.

  **Example**:
  ```
  import logging

  # Configure logging
  logging.basicConfig(level=logging.DEBUG, format='%(asctime)s - %(levelname)s - %(message)s')

  def complex_function(x):
      logging.debug(f"x at start: {x}")
      y = x * 2
      logging.info(f"y after multiplication: {y}")
      z = y + 5
      logging.warning(f"z after addition: {z}")
      return z

  complex_function(10)
  ```
  Output in the console:
  ```s
  2024-08-30 12:00:00 - DEBUG - x at start: 10
  2024-08-30 12:00:00 - INFO - y after multiplication: 20
  2024-08-30 12:00:00 - WARNING - z after addition: 25
  ```

  Logging levels include `DEBUG`, `INFO`, `WARNING`, `ERROR`, and `CRITICAL`, which can be used to differentiate the severity of log messages.
