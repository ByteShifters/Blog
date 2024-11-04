---
title: "Web Development with Python"
tags: ['Python']
date: 2024-09-11
draft: false
toc: true
unlisted: true
---

### **Introduction to Web Frameworks**

- **Understanding Web Frameworks**  
  Web frameworks simplify the process of building web applications by providing a structure and tools for common web development tasks.

  - **Flask**: A lightweight, micro-framework that gives you the flexibility to structure your app as you want.
  - **Django**: A full-fledged framework that comes with built-in features and a "batteries-included" philosophy, providing an out-of-the-box admin interface, ORM, and more.

- **Setting Up a Flask or Django Project**  
  Depending on your choice, here's how to get started:

  - **Flask Setup**:
    ```
    pip install Flask

    # Create a basic Flask app (app.py)
    from flask import Flask

    app = Flask(__name__)

    @app.route('/')
    def home():
        return "Hello, Flask!"

    if __name__ == '__main__':
        app.run(debug=True)
    ```

  - **Django Setup**:
    ```
    pip install Django

    # Create a Django project
    django-admin startproject myproject

    # Navigate to the project directory
    cd myproject

    # Start the development server
    python manage.py runserver

    # Access your Django app at http://127.0.0.1:8000/
    ```

### **Flask Basics**

- **Routes, Views, Templates, and Static Files**  
  Flask routes map URLs to Python functions, known as views, which return the content to be displayed. Templates are used to separate the content from the presentation, and static files like CSS and JavaScript are served from a dedicated directory.

  **Example**:
  ```
  from flask import Flask, render_template

  app = Flask(__name__)

  @app.route('/')
  def home():
      return render_template('index.html')

  if __name__ == '__main__':
      app.run(debug=True)
  ```

  Place your HTML files in a `templates` directory and static assets in a `static` directory.

- **Handling Forms and User Input**  
  Flask handles form data using the `request` object.

  **Example**:
  ```
  from flask import Flask, render_template, request

  app = Flask(__name__)

  @app.route('/', methods=['GET', 'POST'])
  def home():
      if request.method == 'POST':
          username = request.form['username']
          return f"Hello, {username}!"
      return render_template('index.html')

  if __name__ == '__main__':
      app.run(debug=True)
  ```

- **Working with Flask Extensions**  
  Flask has a rich ecosystem of extensions for adding functionality like database support, authentication, and more.

  **Example**: Using `Flask-SQLAlchemy` for database interaction:
  ```
  from flask import Flask
  from flask_sqlalchemy import SQLAlchemy

  app = Flask(__name__)
  app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///example.db'
  db = SQLAlchemy(app)

  class User(db.Model):
      id = db.Column(db.Integer, primary_key=True)
      name = db.Column(db.String(80), nullable=False)

  db.create_all()

  @app.route('/')
  def home():
      users = User.query.all()
      return f"Users: {users}"

  if __name__ == '__main__':
      app.run(debug=True)
  ```

### **Django Basics**

- **Django Project Structure**  
  Django projects are structured with a clear separation of concerns, making it easy to scale and maintain applications. The key components are:

  - **`manage.py`**: A command-line utility for administrative tasks.
  - **`settings.py`**: Configuration for the Django project.
  - **`urls.py`**: URL routing configuration.
  - **Apps**: Modular components of a Django project.

- **Models, Views, and Templates (MVT Pattern)**  
  Django follows the MVT (Model-View-Template) pattern:

  - **Models**: Represent the data structure of your application.
    **Example**:
    ```
    from django.db import models

    class User(models.Model):
        name = models.CharField(max_length=100)
        age = models.IntegerField()
    ```
  
  - **Views**: Handle the logic for processing requests and returning responses.
    **Example**:
    ```
    from django.shortcuts import render
    from .models import User

    def home(request):
        users = User.objects.all()
        return render(request, 'index.html', {'users': users})
    ```

  - **Templates**: Define the HTML structure and presentation.
    ```
    <!-- index.html -->
    <!DOCTYPE html>
    <html>
    <head>
        <title>User List</title>
    </head>
    <body>
        <h1>Users</h1>
        <ul>
            {% for user in users %}
                <li>{{ user.name }} ({{ user.age }} years old)</li>
            {% endfor %}
        </ul>
    </body>
    </html>
    ```

- **Django ORM and Migrations**  
  Django ORM allows you to interact with the database using Python objects, and migrations are used to propagate changes to your models to the database schema.

  **Example**:
  ```
  # After defining your models, create and apply migrations
  python manage.py makemigrations
  python manage.py migrate

  # Now you can interact with the database through Django's ORM
  from myapp.models import User
  User.objects.create(name='Alice', age=30)
  users = User.objects.all()
  ```

### **RESTful APIs**

- **Building REST APIs with Flask**  
  Flask can be used to build RESTful APIs by defining routes that return JSON responses.

  **Example**:
  ```
  from flask import Flask, jsonify, request

  app = Flask(__name__)

  users = []

  @app.route('/users', methods=['GET'])
  def get_users():
      return jsonify(users)

  @app.route('/users', methods=['POST'])
  def add_user():
      user = request.get_json()
      users.append(user)
      return jsonify(user), 201

  if __name__ == '__main__':
      app.run(debug=True)
  ```

- **Building REST APIs with Django REST Framework**  
  Django REST Framework (DRF) is a powerful toolkit for building Web APIs in Django.

  **Example**:
  ```
  # Install Django REST Framework
  pip install djangorestframework

  # Add 'rest_framework' to your INSTALLED_APPS in settings.py

  from rest_framework import serializers, viewsets
  from myapp.models import User

  class UserSerializer(serializers.ModelSerializer):
      class Meta:
          model = User
          fields = ['id', 'name', 'age']

  class UserViewSet(viewsets.ModelViewSet):
      queryset = User.objects.all()
      serializer_class = UserSerializer

  # In urls.py, register the viewset with a router
  from rest_framework.routers import DefaultRouter
  from myapp.views import UserViewSet

  router = DefaultRouter()
  router.register(r'users', UserViewSet)

  urlpatterns = [
      path('', include(router.urls)),
  ]
  ```

- **Understanding HTTP Methods**  
  RESTful APIs typically use the following HTTP methods:

  - **`GET`**: Retrieve data from the server.
  - **`POST`**: Submit new data to the server.
  - **`PUT`**: Update existing data on the server.
  - **`DELETE`**: Remove data from the server.

  **Example**:
  ```
  @app.route('/users/<int:id>', methods=['GET', 'PUT', 'DELETE'])
  def handle_user(id):
      if request.method == 'GET':
          user = next((u for u in users if u['id'] == id), None)
          if user:
              return jsonify(user)
          return jsonify({'error': 'User not found'}), 404

      if request.method == 'PUT':
          user = next((u for u in users if u['id'] == id), None)
          if user:
              user.update(request.get_json())
              return jsonify(user)
          return jsonify({'error': 'User not found'}), 404

      if request.method == 'DELETE':
          global users
          users = [u for u in users if u['id'] != id]
          return '', 204
  ```

- **JSON Serialization and Deserialization**  
  JSON (JavaScript Object Notation) is commonly used for data exchange in web applications. Python provides built-in support for JSON through the `json` module.

  **Example**:
  ```
  import json

  # Serialization: Python object to JSON string
  user = {'name': 'Alice', 'age': 30}
  user_json = json.dumps(user)
  print(user_json)  # Output: {"name": "Alice", "age": 30}

  # Deserialization: JSON string to Python object
  user_data = json.loads(user_json)
  print(user_data)  # Output: {'name': 'Alice', 'age': 30}
  ```
