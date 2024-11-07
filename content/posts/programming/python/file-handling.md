---
title: "File Handling"
tags: ['Python']
date: 2024-09-11
draft: false
toc: true
unlisted: true
author: 'Ren'
authorlink: 'https://about.0x0060.dev/'
---


### **Reading and Writing Files**
Python provides built-in functions and methods to work with files, allowing you to read from and write to them.

- **Opening Files**  
  You can open a file using the `open()` function. The `open()` function takes two main arguments: the filename and the mode in which you want to open the file.

  **Modes**:
  - `'r'`: Read mode (default mode). Opens the file for reading.
  - `'w'`: Write mode. Opens the file for writing (creates a new file if it doesn't exist or truncates the file if it does).
  - `'a'`: Append mode. Opens the file for writing, appending new content to the end of the file.
  - `'b'`: Binary mode. Used with `r`, `w`, or `a` to open the file in binary mode.

  **Example**:
  ```
  file = open("example.txt", "r")  # Opens the file in read mode
  ```

- **Reading Files**  
  Once a file is opened, you can read its contents using various methods.

  - **`read()`**: Reads the entire content of the file as a single string.
    ```
    content = file.read()  # Reads the entire file content
    ```
    
  - **`readline()`**: Reads a single line from the file.
    ```
    line = file.readline()  # Reads the first line of the file
    ```
    
  - **`readlines()`**: Reads all lines in the file and returns a list of strings.
    ```
    lines = file.readlines()  # Reads all lines and returns a list
    ```
  
  **Always close the file after reading:**
  ```
  file.close()  # Closes the file
  ```

- **Writing to Files**  
  You can write data to a file using the `write()` and `writelines()` methods.

  - **`write()`**: Writes a single string to the file.
    ```
    file = open("example.txt", "w")  # Opens the file in write mode
    file.write("Hello, World!")
    file.close()  # Closes the file
    ```
  
  - **`writelines()`**: Writes a list of strings to the file.
    ```
    lines = ["Hello, World!\n", "Python is great!\n"]
    file = open("example.txt", "w")
    file.writelines(lines)
    file.close()
    ```

- **Working with CSV Files**
  Python's `csv` module makes it easy to work with CSV (Comma-Separated Values) files.

  **Example**: Reading from a CSV file
  ```
  import csv

  with open("data.csv", "r") as file:
      reader = csv.reader(file)
      for row in reader:
          print(row)
  ```
  
  **Example**: Writing to a CSV file
  ```
  import csv

  with open("data.csv", "w", newline='') as file:
      writer = csv.writer(file)
      writer.writerow(["Name", "Age", "City"])
      writer.writerow(["Alice", 30, "New York"])
  ```

  **Using `DictReader` and `DictWriter`**:
  - **`DictReader`**: Reads each row of the CSV file as a dictionary.
    ```
    import csv

    with open("data.csv", "r") as file:
        reader = csv.DictReader(file)
        for row in reader:
            print(row)
    ```
    
  - **`DictWriter`**: Writes dictionaries to the CSV file, where keys are treated as field names.
    ```
    import csv

    with open("data.csv", "w", newline='') as file:
        fieldnames = ["Name", "Age", "City"]
        writer = csv.DictWriter(file, fieldnames=fieldnames)

        writer.writeheader()  # Writes the header
        writer.writerow({"Name": "Alice", "Age": 30, "City": "New York"})
    ```
