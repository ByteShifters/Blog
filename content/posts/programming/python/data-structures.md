---
title: "Data Structures in Python"
tags: ['Python']
date: 2024-09-11
draft: false
toc: true
unlisted: true
author: 'Ren'
authorlink: 'https://about.0x0060.dev/'
---

### **Lists**
Lists are ordered collections of items which can be of different types.

- **Creating Lists**  
  You can create a list by placing items inside square brackets `[]`, separated by commas.
  ```
  my_list = [1, 2, 3, "apple", "banana"]
  ```
  
- **Accessing Elements**  
  You can access elements of a list using index numbers, starting from `0` for the first element.
  ```
  first_item = my_list[0]  # first_item is 1
  ```
  
- **Slicing**  
  You can extract a part of a list by slicing it. Slicing is done by specifying a range of indices.
  ```
  sub_list = my_list[1:4]  # sub_list is [2, 3, "apple"]
  ```

- **List Methods**
  - **`append()`**: Adds an item to the end of the list.
    ```
    my_list.append("cherry")  # my_list becomes [1, 2, 3, "apple", "banana", "cherry"]
    ```
    
  - **`extend()`**: Adds elements from another list to the end of the current list.
    ```
    my_list.extend([4, 5])  # my_list becomes [1, 2, 3, "apple", "banana", "cherry", 4, 5]
    ```
    
  - **`insert()`**: Inserts an item at a specified index.
    ```
    my_list.insert(1, "orange")  # my_list becomes [1, "orange", 2, 3, "apple", "banana", "cherry", 4, 5]
    ```
    
  - **`remove()`**: Removes the first occurrence of an item.
    ```
    my_list.remove("apple")  # my_list becomes [1, "orange", 2, 3, "banana", "cherry", 4, 5]
    ```
    
  - **`pop()`**: Removes and returns the item at a specified index. If no index is specified, it removes and returns the last item.
    ```
    last_item = my_list.pop()  # last_item is 5; my_list becomes [1, "orange", 2, 3, "banana", "cherry", 4]
    ```
    
  - **`sort()`**: Sorts the list in ascending order. Works with lists of numbers or strings.
    ```
    my_list = [3, 1, 4, 2]
    my_list.sort()  # my_list becomes [1, 2, 3, 4]
    ```
    
  - **`reverse()`**: Reverses the order of the list.
    ```
    my_list.reverse()  # my_list becomes [4, 3, 2, 1]
    ```

### **Tuples**
Tuples are similar to lists but are immutable (cannot be changed after creation).

- **Creating Tuples**  
  You can create a tuple by placing items inside parentheses `()`, separated by commas.
  ```
  my_tuple = (1, 2, 3, "apple", "banana")
  ```
  
- **Accessing Elements**  
  Accessing elements in a tuple is the same as with lists, using index numbers.
  ```
  first_item = my_tuple[0]  # first_item is 1
  ```
  
- **Tuple Operations**  
  Although tuples are immutable, you can perform operations like concatenation and repetition.
  ```
  new_tuple = my_tuple + ("cherry",)  # new_tuple is (1, 2, 3, "apple", "banana", "cherry")
  repeated_tuple = my_tuple * 2  # repeated_tuple is (1, 2, 3, "apple", "banana", 1, 2, 3, "apple", "banana")
  ```

### **Dictionaries**
Dictionaries are unordered collections of key-value pairs.

- **Creating Dictionaries**  
  You can create a dictionary by placing key-value pairs inside curly braces `{}`, separated by commas.
  ```
  my_dict = {"name": "Alice", "age": 25, "city": "New York"}
  ```
  
- **Accessing Values**  
  You can access values in a dictionary by using their corresponding keys.
  ```
  name = my_dict["name"]  # name is "Alice"
  ```

- **Dictionary Methods**
  - **`get()`**: Returns the value for a specified key. If the key does not exist, it returns `None` or a specified default value.
    ```
    age = my_dict.get("age")  # age is 25
    country = my_dict.get("country", "Unknown")  # country is "Unknown"
    ```
    
  - **`keys()`**: Returns a view object that displays a list of all the keys in the dictionary.
    ```
    keys = my_dict.keys()  # keys is dict_keys(['name', 'age', 'city'])
    ```
    
  - **`values()`**: Returns a view object that displays a list of all the values in the dictionary.
    ```
    values = my_dict.values()  # values is dict_values(['Alice', 25, 'New York'])
    ```
    
  - **`items()`**: Returns a view object that displays a list of key-value pairs as tuples.
    ```
    items = my_dict.items()  # items is dict_items([('name', 'Alice'), ('age', 25), ('city', 'New York')])
    ```
    
  - **`update()`**: Updates the dictionary with elements from another dictionary or an iterable of key-value pairs.
    ```
    my_dict.update({"age": 26, "country": "USA"})  # my_dict becomes {'name': 'Alice', 'age': 26, 'city': 'New York', 'country': 'USA'}
    ```

### **Sets**
Sets are unordered collections of unique items.

- **Creating Sets**  
  You can create a set by placing items inside curly braces `{}`, separated by commas.
  ```
  my_set = {1, 2, 3, "apple", "banana"}
  ```
  
- **Basic Operations**
  Sets automatically remove duplicates, and you cannot access elements by index because they are unordered.
  ```
  my_set = {1, 2, 3, 3, "apple", "banana"}  # my_set becomes {1, 2, 3, 'apple', 'banana'}
  ```

- **Set Methods**
  - **`add()`**: Adds an element to the set.
    ```
    my_set.add("cherry")  # my_set becomes {1, 2, 3, 'apple', 'banana', 'cherry'}
    ```
    
  - **`remove()`**: Removes a specified element from the set. Raises a `KeyError` if the element is not found.
    ```
    my_set.remove("banana")  # my_set becomes {1, 2, 3, 'apple', 'cherry'}
    ```
    
  - **`union()`**: Returns a new set containing all elements from both sets (no duplicates).
    ```
    set1 = {1, 2, 3}
    set2 = {3, 4, 5}
    union_set = set1.union(set2)  # union_set is {1, 2, 3, 4, 5}
    ```
    
  - **`intersection()`**: Returns a new set containing only the elements that are common to both sets.
    ```
    intersection_set = set1.intersection(set2)  # intersection_set is {3}
    ```
