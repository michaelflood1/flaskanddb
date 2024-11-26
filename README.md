# Flask

What is flask

- a microframework: it does most of the heavy work dealing with http requests/responses
- it has additional support for automatic json serialization/deserialization
- easy to use and has a development server to run and test code on your pc

    pip install flask

    from flask import Flask
    app = Flask(__name__)

    if __name__ == "__main__"
    app.run(debug=True)

### working with flask

To work with your application use the decorator @app.route("/something")

the return value of the function will be sent as the http response


    @app.route("/example")
    def example():
    return "I am running Flask!"

The function above defines what happens when someone tries to browse to /example

### Templates

You could render the whole HTML page in the return statement, but it is very impractical.

A better way is to use the render_template method. It allows to create an HTML template. The template can

contain variables and basic logic, and will be interpolated at runtime.

Use render_template('my_template.html', variable1=value1) .

Flask will look for my_template.html in the templates folder.

In that template, all references to {{ variable1 }} will be replaced by value1

### Log in templates

It is possible to insert some logic in the templates, instead of the views. This allows to reuse the same template
for different views.

Flask uses Jinja2 for its templates. The documentation has many useful examples

### a simple flask app with a template

template/base.html

    <html>
        <body>
            <!-- The value will change depending on the variable sent to the template -->
            Hello, {{ username }}! Here are some numbers from 1 to 5:
            <ul>{% for x in range(1, 6) %}<li>{{ x }}</li>{% endfor %}</ul>
        </body>
    </html>

app.py

    from flask import Flask, render_template
    app = Flask(__name__)
    @app.route('/')
    def homepage():
    return render_template('base.html', username="John Doe")
    if __name__ == "__main__":
    app.run()

### dictionary unpacking

    render_template("index.html", var1=value1, var2=value2)

**The following is better and easier to manage**

    context = {"var1": value1, "var2": value2}
    render_template("index.html", **context

### routes with arguments

You may want to define a route that takes an argument.

For instance /student/1234/ that displays information about student 1234

    @app.route("/student/<int:student_id>")
    def show_student(student_id):
        return render_template(...)

Notice how the function now takes an argument: the value received in the URL.

The list of converters is available on Flask's website

### flask converters for url rules

|item|type|
|--|--|
|string|default type accepts any item without slash|
|int|positive integers|
|float|positive integers|
|path| like strings but accepts slashes|
|uuid| accepts UUID strings|

A Universally Unique Identifier (UUID) is a 128-bit label used to uniquely identify objects in computer systems

### reversing urls in templates or python code

Python

    from flask import url_for
    link = url_for("show_student", student_id=1234)

in a jinja template

    <a href="{{ url_for('show_student', student_id=1234) }}">Student 1234</a>

### template scaffolding

as much as you can reuse code in python you can create reusable templates

generally there is a base template that other templates can build upon

templates/base.html <-- base template

    <html>
    <head><title>Example</title></head>
    <body><div class="container">
    {% block main %}
    {% endblock %}
    </div></body>
    </html>

templates/home.html <-- builds upon base

    {% extends "base.html" %}
    {% block main %}
    <h1>Welcome to my Flask website</h1>
    {% endblock %}

templates/student.html

    {% extends "base.html" %}
    {% block main %}
    <h1>Student {{ student_id }}</h1>
    {% endblock %}

### HTTP error codes with flask

in a view you can also return a tuple

the first element is the response the second is the status code

    @app.route('/data/<value>')
    def show_data(value):
    if value == "notfound":
    # We specify the status code in the return value
    return render_template('error.html'), 404
    else:
    # By default, Flask uses 200 (or 204)
    return render_template('page.html')

### flask project work 


## General Flask Tips
- **Debug Mode:**
  - Running in debug mode (`debug=True`) automatically reloads the app when you make changes to the code.  
  - Don’t forget to refresh the browser when you make changes.

- **Local Access Only:**
  - By default, the app listens only on local interfaces (`127.0.0.1` or `localhost`), so it's not available to others unless explicitly configured.

- **Avoid `flask run`:**
  - While `flask run` can start the app, it's better to use `python app.py` for consistency unless you fully understand your terminal settings and system configuration.

## Routes and URLs
- **Routes Are Functions:**
  - A route in Flask corresponds to a Python function.
  - The function must return at least one value, typically a string or an HTML response.

- **Change Route Outputs Dynamically:**
  - Experiment with returning raw HTML or plain text in your route functions to observe the results.

## Templates and Best Practices
- **Separation of Concerns:**
  - Avoid returning raw HTML from Python functions; instead, use templates to keep your code and HTML separate.

- **Default Template Folder:**
  - Flask looks for templates in a folder named `templates` by default. Make sure to keep your HTML files there.

- **Dynamic Content in Templates:**
  - Use `{{ ... }}` for placeholders (e.g., `{{ name }}`).
  - Use `{% ... %}` for logic, such as loops (`{% for ... %}`) and conditionals.

- **Template Inheritance:**
  - Use `base.html` as a scaffold for other templates to define consistent structure (e.g., header, footer, nav menu).
  - Child templates should override only specific blocks like `{% block content %}`.

## Data Handling
- **Use `csv.DictReader`:**
  - Reading CSV files with `csv.DictReader` simplifies working with the data by returning dictionaries for each row.
  - Access attributes in the template directly with dot notation (e.g., `user.name` for a dictionary key `name`).

- **Data File Placement:**
  - Place all CSV files in a `data` folder for better organization.

## HTML and CSS
- **Static Files:**
  - Store CSS, JS, and other static resources in a `static` folder.
  - Link static files in templates using Flask’s `url_for` function:
    ```html
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
    ```

- **CSS for Styling:**
  - Define global CSS in `base.html` inside the `<head>` section.
  - Use `class` or `id` attributes in your HTML for targeted styling.

## Miscellaneous
- **Experiment and Explore:**
  - Try modifying template content and structure to understand how Flask dynamically renders HTML.
  - Test adding new routes and templates to explore Flask’s flexibility.


    /flask_app/
    ├── app.py
    ├── /templates/
    │     ├── base.html
    │     ├── home.html
    │     ├── users.html
    │     ├── books.html
    ├── /static/
    │     ├── style.css
    ├── /data/
            ├── users.csv
            ├── books.csv


pip install flask

```py
    from flask import Flask, render_template
    import csv

    app = Flask(__name__)

    @app.route("/")
    def home():
        return render_template("home.html")

    @app.route("/users")
    def user_list():
        # Read users.csv
        with open("data/users.csv") as f:
            reader = csv.DictReader(f)
            users = [row for row in reader]
        return render_template("users.html", users=users)

    @app.route("/books")
    def book_list():
        # Read books.csv
        with open("data/books.csv") as f:
            reader = csv.DictReader(f)
            books = [row for row in reader]
        return render_template("books.html", books=books)

    if __name__ == "__main__":
        app.run(debug=True, port=8888)
```
base.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flask App</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
    <nav>
        <a href="{{ url_for('home') }}">Home</a>
        <a href="{{ url_for('user_list') }}">Users</a>
        <a href="{{ url_for('book_list') }}">Books</a>
    </nav>
    <main>
        {% block content %}{% endblock %}
    </main>
</body>
</html>
```

home.html

```html
{% extends "base.html" %}
{% block content %}
<h1>Welcome to the Flask App!</h1>
<p>This is the homepage.</p>
{% endblock %}
```
users.html
```html
{% extends "base.html" %}
{% block content %}
<h1>User List</h1>
<ul>
    {% for user in users %}
        <li>{{ user['name'] }} - {{ user['phone'] }}</li>
    {% endfor %}
</ul>
{% endblock %}
```

books.html
```html
{% extends "base.html" %}
{% block content %}
<h1>Book List</h1>
<ul>
    {% for book in books %}
        <li>{{ book['title'] }} by {{ book['author'] }}</li>
    {% endfor %}
</ul>
{% endblock %}
```

data/users.csv

```python
name,phone
John Doe,123-456-7890
Jane Smith,987-654-3210
```
data/books.csv
```python
title,author
The Great Gatsby,F. Scott Fitzgerald
1984,George Orwell
```

app.py updated
```py
from flask import Flask, render_template
import csv

app = Flask(__name__)

# Route for the homepage
@app.route("/")
def home():
    return render_template("home.html")

# Route for the users list
@app.route("/users")
def user_list():
    # Read users.csv from the data folder
    with open("data/users.csv") as f:
        reader = csv.DictReader(f)
        users = [row for row in reader]  # Convert rows into a list of dictionaries
    return render_template("users.html", users=users)

# Route for the books list
@app.route("/books")
def book_list():
    # Read books.csv from the data folder
    with open("data/books.csv") as f:
        reader = csv.DictReader(f)
        books = [row for row in reader]  # Convert rows into a list of dictionaries
    return render_template("books.html", books=books)

if __name__ == "__main__":
    app.run(debug=True, port=8888)
```

# week 12

## week 12 lab

pip install flask-sqlalchemy

app.py
```py
app = Flask(__name__)
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///i_copy_pasted_this.db"
app.instance_path = Path("change_this").resolve()  # Adjust as needed

db.init_app(app)

```

db.py
```py
from flask_sqlalchemy import SQLAlchemy
from sqlalchemy.orm import DeclarativeBase

class Base(DeclarativeBase):
    pass

db = SQLAlchemy(model_class=Base)
```

models.py
```py
class Book(db.Model):
    id = db.mapped_column(Integer, primary_key=True)
    title = db.mapped_column(String(255), nullable=False)
    price = db.mapped_column(DECIMAL(10, 2), nullable=False)
    available = db.mapped_column(Integer, default=0)
    category_id = db.mapped_column(Integer, ForeignKey("category.id"), nullable=True)
    
    # Relationship to the Category model
    category = relationship("Category", back_populates="books")
    rentals = relationship("BookRental", back_populates="book")


class Category(db.Model):
    id = db.mapped_column(Integer, primary_key=True)
    name = db.mapped_column(String(100), unique=True, nullable=False)

    # Relationship to the Book model
    books = relationship("Book", back_populates="category")


class BookRental(db.Model):
    id = db.mapped_column(Integer, primary_key=True)
    book_id = db.mapped_column(Integer, ForeignKey("book.id"), nullable=False)
    user_id = db.mapped_column(Integer, ForeignKey("user.id"), nullable=False)
    rented = db.mapped_column(String)  # Assuming a datetime string
    returned = db.mapped_column(String, nullable=True)

    # Relationships
    book = relationship("Book", back_populates="rentals")
    user = relationship("User", back_populates="rentals")


class User(db.Model):
    id = db.mapped_column(Integer, primary_key=True)
    name = db.mapped_column(String(100), unique=True, nullable=False)

    # Relationship to the BookRental model
    rentals = relationship("BookRental", back_populates="user")

```
manage.py
```py
from app import app  # Import your Flask app
from db import db    # Import your SQLAlchemy instance
from models import Book, Category, User, BookRental  # Import models
import csv

# Drop all tables
def drop_all_tables():
    with app.app_context():
        db.drop_all()
        print("Dropped all tables!")

# Create all tables
def create_all_tables():
    with app.app_context():
        db.create_all()
        print("Created all tables!")

# Seed the database
def seed_data():
    with app.app_context():
        # Seed categories
        categories = ["Travel", "Fun", "History"]
        for category_name in categories:
            category = Category(name=category_name)
            db.session.add(category)
        
        # Seed books
        books = [
            {"title": "Journey to the West", "price": 19.99, "available": 10, "category_id": 1},
            {"title": "Adventures in Wonderland", "price": 14.99, "available": 5, "category_id": 2},
        ]
        for book_data in books:
            book = Book(**book_data)
            db.session.add(book)

        # Seed users
        users = ["Alice", "Bob", "Charlie"]
        for user_name in users:
            user = User(name=user_name)
            db.session.add(user)
        
        # Commit all changes
        db.session.commit()
        print("Seeded database with initial data!")

# Import data from CSV files
def import_data_from_csv():
    with app.app_context():
        # Example for books.csv
        with open("books.csv", "r") as file:
            reader = csv.DictReader(file)
            for row in reader:
                book = Book(
                    title=row["title"],
                    price=float(row["price"]),
                    available=int(row["available"]),
                    category_id=int(row["category_id"])
                )
                db.session.add(book)

        db.session.commit()
        print("Imported data from CSV files!")

# Main block for execution
if __name__ == "__main__":
    print("Choose an option:")
    print("1. Drop all tables")
    print("2. Create all tables")
    print("3. Seed database")
    print("4. Import data from CSV")
    
    choice = input("Enter choice: ")
    if choice == "1":
        drop_all_tables()
    elif choice == "2":
        create_all_tables()
    elif choice == "3":
        seed_data()
    elif choice == "4":
        import_data_from_csv()
    else:
        print("Invalid choice!")

    seed_data()
```
python manage.py
