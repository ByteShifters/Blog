---
title: "Advanced Python Concepts"
tags: ['Python']
date: 2024-09-11
draft: false
toc: true
unlisted: true
---

### **Decorators**
Decorators are a way to modify or extend the behavior of functions or classes without changing their code.

- **Function Decorators**  
  Function decorators are used to wrap another function, allowing you to execute code before or after the wrapped function, or even modify its behavior.

  **Example**:
  ```
  def my_decorator(func):
      def wrapper():
          print("Something is happening before the function is called.")
          func()
          print("Something is happening after the function is called.")
      return wrapper

  @my_decorator
  def say_hello():
      print("Hello!")

  say_hello()
  # Output:
  # Something is happening before the function is called.
  # Hello!
  # Something is happening after the function is called.
  ```

- **Class Decorators**  
  Class decorators work similarly to function decorators but are applied to classes.

  **Example**:
  ```
  def class_decorator(cls):
      cls.decorated = True
      return cls

  @class_decorator
  class MyClass:
      pass

  print(MyClass.decorated)  # Output: True
  ```

- **Built-in Decorators**  
  Python provides several built-in decorators:

  - **`@property`**: Defines a method as a property, allowing access like an attribute.
    ```
    class MyClass:
        def __init__(self, value):
            self._value = value

        @property
        def value(self):
            return self._value

        @value.setter
        def value(self, new_value):
            self._value = new_value

    obj = MyClass(10)
    print(obj.value)  # Output: 10
    obj.value = 20
    print(obj.value)  # Output: 20
    ```

  - **`@staticmethod`**: Defines a method that does not require access to the instance (`self`) or class (`cls`).
    ```
    class Math:
        @staticmethod
        def add(x, y):
            return x + y

    print(Math.add(5, 3))  # Output: 8
    ```

  - **`@classmethod`**: Defines a method that takes the class as its first argument (`cls`), not the instance.
    ```
    class MyClass:
        @classmethod
        def create_instance(cls):
            return cls()

    instance = MyClass.create_instance()
    print(type(instance))  # Output: <class '__main__.MyClass'>
    ```

### **Generators and Iterators**

- **Creating Iterators**  
  An iterator is an object that implements the `__iter__()` and `__next__()` methods. 

  **Example**:
  ```
  class MyIterator:
      def __init__(self, start, end):
          self.current = start
          self.end = end

      def __iter__(self):
          return self

      def __next__(self):
          if self.current > self.end:
              raise StopIteration
          self.current += 1
          return self.current - 1

  iterator = MyIterator(1, 5)
  for number in iterator:
      print(number)
  # Output: 1 2 3 4 5
  ```

- **Generator Functions**  
  Generators use the `yield` keyword to produce a sequence of values lazily. They simplify iterator creation and manage state automatically.

  **Example**:
  ```
  def count_up_to(max):
      count = 1
      while count <= max:
          yield count
          count += 1

  for number in count_up_to(5):
      print(number)
  # Output: 1 2 3 4 5
  ```

### **Context Managers**
Context managers are used to manage resources like file streams or network connections. They ensure that resources are properly cleaned up after use.

- **Using `with` Statement**  
  The `with` statement simplifies exception handling and resource management by automatically handling setup and teardown.

  **Example**:
  ```
  with open("example.txt", "w") as file:
      file.write("Hello, world!")
  # The file is automatically closed after the block is executed.
  ```

- **Creating Custom Context Managers**  
  You can create custom context managers by defining a class with `__enter__` and `__exit__` methods.

  **Example**:
  ```
  class MyContextManager:
      def __enter__(self):
          print("Entering the context")
          return self

      def __exit__(self, exc_type, exc_value, traceback):
          print("Exiting the context")

  with MyContextManager() as cm:
      print("Inside the context")
  # Output:
  # Entering the context
  # Inside the context
  # Exiting the context
  ```

### **Comprehensions**
Comprehensions provide a concise way to create lists, dictionaries, or sets from iterable data.

- **List Comprehensions**  
  List comprehensions allow you to create lists using a single line of code.

  **Example**:
  ```
  numbers = [1, 2, 3, 4, 5]
  squares = [x**2 for x in numbers]
  print(squares)  # Output: [1, 4, 9, 16, 25]
  ```

- **Dictionary Comprehensions**  
  Dictionary comprehensions create dictionaries using a similar syntax to list comprehensions.

  **Example**:
  ```
  numbers = [1, 2, 3, 4, 5]
  square_dict = {x: x**2 for x in numbers}
  print(square_dict)  # Output: {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
  ```

- **Set Comprehensions**  
  Set comprehensions generate sets in a concise manner.

  **Example**:
  ```
  numbers = [1, 2, 3, 4, 5]
  square_set = {x**2 for x in numbers}
  print(square_set)  # Output: {1, 4, 9, 16, 25}
  ```
