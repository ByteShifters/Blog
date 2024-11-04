---
title: "Object-Oriented Programming (OOP)"
tags: ['Python']
date: 2024-09-11
draft: false
toc: true
unlisted: true
---

### **Classes and Objects**
Classes and objects are fundamental concepts in OOP. A class defines a blueprint for creating objects, while an object is an instance of a class.

- **Defining Classes and Creating Objects**  
  A class is defined using the `class` keyword. Objects are created by calling the class as if it were a function.

  **Example**:
  ```
  class Dog:
      def __init__(self, name, age):
          self.name = name
          self.age = age

      def bark(self):
          return f"{self.name} barks!"

  my_dog = Dog("Buddy", 3)
  print(my_dog.bark())  # Output: Buddy barks!
  ```

- **Understanding `self` and `__init__` Method**  
  - **`self`**: Refers to the instance of the class and is used to access attributes and methods of the class.
  - **`__init__`**: The constructor method that is called when an object is instantiated. It initializes the object's attributes.

  **Example**:
  ```
  class Cat:
      def __init__(self, name, color):
          self.name = name
          self.color = color

      def meow(self):
          return f"{self.name} says meow!"

  my_cat = Cat("Whiskers", "gray")
  print(my_cat.meow())  # Output: Whiskers says meow!
  ```

### **Class Attributes and Methods**
Class attributes and methods belong to the class itself rather than any individual object.

- **Instance Attributes and Methods**  
  Instance attributes are specific to an object, while instance methods operate on those attributes.

  **Example**:
  ```
  class Person:
      def __init__(self, name, age):
          self.name = name
          self.age = age

      def greet(self):
          return f"Hello, my name is {self.name} and I am {self.age} years old."

  person = Person("Alice", 30)
  print(person.greet())  # Output: Hello, my name is Alice and I am 30 years old.
  ```

- **Class Attributes and Methods**  
  - **Class Attributes**: Shared across all instances of the class.
    ```
    class Dog:
        species = "Canis familiaris"  # Class attribute

        def __init__(self, name, age):
            self.name = name
            self.age = age

    print(Dog.species)  # Output: Canis familiaris
    ```

  - **Class Methods**: Defined with the `@classmethod` decorator, take `cls` as the first parameter.
    ```
    class Dog:
        species = "Canis familiaris"

        @classmethod
        def get_species(cls):
            return cls.species

    print(Dog.get_species())  # Output: Canis familiaris
    ```

  - **Static Methods**: Defined with the `@staticmethod` decorator, do not take `self` or `cls` as parameters.
    ```
    class Math:
        @staticmethod
        def add(x, y):
            return x + y

    print(Math.add(5, 3))  # Output: 8
    ```

### **Inheritance**
Inheritance allows one class (subclass) to inherit attributes and methods from another class (superclass).

- **Inheriting Classes and Method Overriding**  
  A subclass can override methods from its superclass to provide specific implementations.

  **Example**:
  ```
  class Animal:
      def speak(self):
          return "Animal speaks"

  class Dog(Animal):
      def speak(self):
          return "Dog barks"

  my_dog = Dog()
  print(my_dog.speak())  # Output: Dog barks
  ```

- **Using `super()` Function**  
  The `super()` function is used to call methods from the superclass.

  **Example**:
  ```
  class Animal:
      def __init__(self, name):
          self.name = name

      def speak(self):
          return "Animal speaks"

  class Dog(Animal):
      def __init__(self, name, breed):
          super().__init__(name)
          self.breed = breed

      def speak(self):
          return f"{super().speak()} and Dog barks"

  my_dog = Dog("Buddy", "Golden Retriever")
  print(my_dog.speak())  # Output: Animal speaks and Dog barks
  ```

### **Encapsulation, Abstraction, and Polymorphism**

- **Encapsulation**  
  Encapsulation is the concept of wrapping data (attributes) and methods (functions) into a single unit (class). It hides the internal state and requires all interaction to be performed through an object's methods.

  **Example**:
  ```
  class Account:
      def __init__(self, balance):
          self.__balance = balance  # Private attribute

      def deposit(self, amount):
          self.__balance += amount

      def get_balance(self):
          return self.__balance

  acc = Account(1000)
  acc.deposit(500)
  print(acc.get_balance())  # Output: 1500
  ```

- **Abstraction**  
  Abstraction involves hiding the complex implementation details and showing only the essential features of an object. In Python, this can be achieved using abstract classes and methods.

  **Example**:
  ```
  from abc import ABC, abstractmethod

  class AbstractShape(ABC):
      @abstractmethod
      def area(self):
          pass

  class Rectangle(AbstractShape):
      def __init__(self, width, height):
          self.width = width
          self.height = height

      def area(self):
          return self.width * self.height

  rect = Rectangle(5, 3)
  print(rect.area())  # Output: 15
  ```

- **Polymorphism**  
  Polymorphism allows objects of different classes to be treated as objects of a common superclass. It also means that different classes can have methods with the same name but different implementations.

  **Example**:
  ```
  class Cat:
      def speak(self):
          return "Meow"

  class Dog:
      def speak(self):
          return "Woof"

  animals = [Cat(), Dog()]

  for animal in animals:
      print(animal.speak())  # Output: Meow \n Woof
  ```

- **Method Overloading and Operator Overloading**  
  - **Method Overloading**: Python does not support method overloading directly, but you can achieve similar behavior using default arguments or variable-length arguments.
  - **Operator Overloading**: You can define special methods in a class to enable the use of operators with objects of that class.

  **Example** (Operator Overloading):
  ```
  class Vector:
      def __init__(self, x, y):
          self.x = x
          self.y = y

      def __add__(self, other):
          return Vector(self.x + other.x, self.y + other.y)

      def __str__(self):
          return f"Vector({self.x}, {self.y})"

  v1 = Vector(1, 2)
  v2 = Vector(3, 4)
  v3 = v1 + v2
  print(v3)  # Output: Vector(4, 6)
  ```  
