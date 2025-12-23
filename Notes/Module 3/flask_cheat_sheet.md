# Flask Web Development Cheat Sheet

## Table of Contents
- [Installation & Setup](#installation--setup)
- [Basic Application Structure](#basic-application-structure)
- [Routing](#routing)
- [Request & Response](#request--response)
- [Templates (Jinja2)](#templates-jinja2)
- [Static Files](#static-files)
- [Forms & Validation](#forms--validation)
- [Database (SQLAlchemy)](#database-sqlalchemy)
- [User Sessions](#user-sessions)
- [Error Handling](#error-handling)
- [Configuration](#configuration)
- [Testing](#testing)
- [Deployment](#deployment)
- [Common Extensions](#common-extensions)

---

## Installation & Setup

### Create Virtual Environment
bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
# On Windows:
venv\Scripts\activate
# On macOS/Linux:
source venv/bin/activate


### Install Flask
bash
# Install specific version
pip install Flask==2.2.2

# Install latest version
pip install Flask

# Save dependencies
pip freeze > requirements.txt

# Install from requirements
pip install -r requirements.txt


### Project Structure

myapp/
├── app.py                 # Main application file
├── requirements.txt       # Dependencies
├── config.py             # Configuration
├── static/               # Static files (CSS, JS, images)
│   ├── css/
│   ├── js/
│   └── images/
├── templates/            # HTML templates
│   ├── base.html
│   ├── index.html
│   └── about.html
├── models/               # Database models
├── routes/               # Route blueprints
└── tests/                # Test files


---

## Basic Application Structure

### Minimal Flask App
python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(debug=True)


### Running the Application
bash
# Method 1: Direct execution
python app.py

# Method 2: Using flask command
export FLASK_APP=app.py
export FLASK_ENV=development
flask run

# Run on specific host and port
flask run --host=0.0.0.0 --port=5000


---

## Routing

### Basic Routes
python
# Simple route
@app.route('/')
def index():
    return 'Index Page'

# Route with variable
@app.route('/user/<username>')
def show_user(username):
    return f'User: {username}'

# Route with typed variable
@app.route('/post/<int:post_id>')
def show_post(post_id):
    return f'Post ID: {post_id}'

# Multiple HTTP methods
@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        return 'Processing login'
    return 'Show login form'


### Variable Types in Routes
python
# String (default)
@app.route('/string/<string:text>')

# Integer
@app.route('/int/<int:number>')

# Float
@app.route('/float/<float:value>')

# Path (accepts slashes)
@app.route('/path/<path:subpath>')

# UUID
@app.route('/uuid/<uuid:id>')


### URL Building
python
from flask import url_for

@app.route('/')
def index():
    return 'Index'

@app.route('/user/<username>')
def profile(username):
    return f'Profile: {username}'

# Build URLs
with app.test_request_context():
    print(url_for('index'))                    # Output: /
    print(url_for('profile', username='john')) # Output: /user/john
    print(url_for('static', filename='style.css'))  # Output: /static/style.css


---

## Request & Response

### Accessing Request Data
python
from flask import request

@app.route('/data', methods=['GET', 'POST'])
def handle_data():
    # Get request method
    method = request.method
    
    # Query parameters (URL arguments)
    search = request.args.get('search', '')
    
    # Form data
    username = request.form.get('username')
    
    # JSON data
    data = request.get_json()
    
    # Headers
    user_agent = request.headers.get('User-Agent')
    
    # Cookies
    session_id = request.cookies.get('session_id')
    
    # Files
    file = request.files.get('file')
    
    return 'Data processed'


### Response Objects
python
from flask import make_response, jsonify, redirect, render_template

# Return string (default: 200 status)
@app.route('/text')
def text():
    return 'Plain text response'

# Return JSON
@app.route('/json')
def json_response():
    return jsonify({'key': 'value', 'status': 'success'})

# Custom status code
@app.route('/not-found')
def not_found():
    return 'Not found', 404

# Custom response with headers
@app.route('/custom')
def custom_response():
    response = make_response('Custom response')
    response.status_code = 200
    response.headers['X-Custom-Header'] = 'Value'
    response.set_cookie('name', 'value')
    return response

# Redirect
@app.route('/old')
def old_route():
    return redirect(url_for('new_route'))

@app.route('/new')
def new_route():
    return 'New route'


---

## Templates (Jinja2)

### Rendering Templates
python
from flask import render_template

@app.route('/hello/<name>')
def hello(name):
    return render_template('hello.html', name=name)

@app.route('/users')
def users():
    user_list = ['Alice', 'Bob', 'Charlie']
    return render_template('users.html', users=user_list)


### Template Syntax

#### Base Template (base.html)
html
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}Default Title{% endblock %}</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
</head>
<body>
    <header>
        {% block header %}
        <h1>My Website</h1>
        {% endblock %}
    </header>
    
    <main>
        {% block content %}{% endblock %}
    </main>
    
    <footer>
        {% block footer %}
        <p>&copy; 2024 My Website</p>
        {% endblock %}
    </footer>
</body>
</html>


#### Child Template (page.html)
html
{% extends "base.html" %}

{% block title %}Page Title{% endblock %}

{% block content %}
<h2>Welcome, {{ name }}!</h2>

{# This is a comment #}

<!-- Variables -->
<p>{{ variable }}</p>
<p>{{ variable|upper }}</p>
<p>{{ variable|default('default value') }}</p>

<!-- If statements -->
{% if user %}
    <p>Hello, {{ user }}!</p>
{% else %}
    <p>Hello, Guest!</p>
{% endif %}

<!-- For loops -->
<ul>
{% for item in items %}
    <li>{{ loop.index }}: {{ item }}</li>
{% endfor %}
</ul>

<!-- Include other templates -->
{% include 'sidebar.html' %}

<!-- Safe HTML rendering -->
{{ content|safe }}

<!-- URL generation -->
<a href="{{ url_for('profile', username=user.name) }}">Profile</a>
{% endblock %}


### Common Jinja2 Filters
html
<!-- String filters -->
{{ text|upper }}              <!-- UPPERCASE -->
{{ text|lower }}              <!-- lowercase -->
{{ text|capitalize }}         <!-- Capitalize first letter -->
{{ text|title }}              <!-- Title Case -->
{{ text|trim }}               <!-- Remove whitespace -->
{{ text|truncate(20) }}       <!-- Truncate to 20 chars -->

<!-- Number filters -->
{{ number|round }}            <!-- Round number -->
{{ number|int }}              <!-- Convert to integer -->
{{ number|float }}            <!-- Convert to float -->

<!-- List filters -->
{{ list|length }}             <!-- Get length -->
{{ list|first }}              <!-- First item -->
{{ list|last }}               <!-- Last item -->
{{ list|join(', ') }}         <!-- Join with separator -->
{{ list|sort }}               <!-- Sort list -->

<!-- Default and safe -->
{{ variable|default('N/A') }} <!-- Default value if None -->
{{ html_content|safe }}       <!-- Mark as safe HTML -->


---

## Static Files

### Serving Static Files
python
# Static files are automatically served from /static directory
# Access in templates:


html
<!-- CSS -->
<link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">

<!-- JavaScript -->
<script src="{{ url_for('static', filename='js/script.js') }}"></script>

<!-- Images -->
<img src="{{ url_for('static', filename='images/logo.png') }}" alt="Logo">

<!-- Custom static folder -->
app = Flask(__name__, static_folder='assets', static_url_path='/assets')


---

## Forms & Validation

### HTML Forms
html
<form method="POST" action="{{ url_for('login') }}">
    <input type="text" name="username" required>
    <input type="password" name="password" required>
    <button type="submit">Login</button>
</form>


### Handling Form Data
python
from flask import request, flash, redirect

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        
        # Validate
        if not username or not password:
            flash('Please fill all fields', 'error')
            return redirect(url_for('login'))
        
        # Process login
        flash('Login successful', 'success')
        return redirect(url_for('dashboard'))
    
    return render_template('login.html')


### Flask-WTF (Extension)
python
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, SubmitField
from wtforms.validators import DataRequired, Email, Length

class LoginForm(FlaskForm):
    username = StringField('Username', validators=[DataRequired(), Length(min=3, max=20)])
    password = PasswordField('Password', validators=[DataRequired(), Length(min=8)])
    submit = SubmitField('Login')

@app.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    if form.validate_on_submit():
        username = form.username.data
        password = form.password.data
        # Process login
        return redirect(url_for('dashboard'))
    return render_template('login.html', form=form)


---

## Database (SQLAlchemy)

### Setup Flask-SQLAlchemy
python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///database.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)


### Define Models
python
from datetime import datetime

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
    posts = db.relationship('Post', backref='author', lazy=True)
    
    def __repr__(self):
        return f'<User {self.username}>'

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    content = db.Column(db.Text, nullable=False)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    
    def __repr__(self):
        return f'<Post {self.title}>'


### Database Operations
python
# Create tables
with app.app_context():
    db.create_all()

# Create
user = User(username='john', email='john@example.com')
db.session.add(user)
db.session.commit()

# Read
users = User.query.all()
user = User.query.filter_by(username='john').first()
user = User.query.get(1)  # Get by ID

# Update
user = User.query.get(1)
user.email = 'newemail@example.com'
db.session.commit()

# Delete
user = User.query.get(1)
db.session.delete(user)
db.session.commit()

# Query methods
User.query.filter_by(username='john').all()
User.query.filter(User.username.like('%john%')).all()
User.query.order_by(User.created_at.desc()).all()
User.query.limit(10).all()
Post.query.join(User).filter(User.username == 'john').all()


---

## User Sessions

### Configure Sessions
python
from flask import session

app.secret_key = 'your-secret-key-here'  # Required for sessions


### Working with Sessions
python
from flask import session

@app.route('/login', methods=['POST'])
def login():
    username = request.form.get('username')
    # Verify credentials...
    
    # Set session data
    session['username'] = username
    session['logged_in'] = True
    
    return redirect(url_for('dashboard'))

@app.route('/dashboard')
def dashboard():
    # Check if user is logged in
    if 'logged_in' not in session:
        return redirect(url_for('login'))
    
    username = session.get('username')
    return render_template('dashboard.html', username=username)

@app.route('/logout')
def logout():
    # Clear session
    session.pop('username', None)
    session.pop('logged_in', None)
    # Or clear all
    session.clear()
    
    return redirect(url_for('index'))


### Flash Messages
python
from flask import flash

@app.route('/login', methods=['POST'])
def login():
    flash('Login successful!', 'success')
    flash('Invalid credentials', 'error')
    flash('Please verify your email', 'warning')
    return redirect(url_for('dashboard'))


html
<!-- Display flash messages in template -->
{% with messages = get_flashed_messages(with_categories=true) %}
    {% if messages %}
        {% for category, message in messages %}
            <div class="alert alert-{{ category }}">{{ message }}</div>
        {% endfor %}
    {% endif %}
{% endwith %}


---

## Error Handling

### Error Handlers
python
@app.errorhandler(404)
def not_found_error(error):
    return render_template('404.html'), 404

@app.errorhandler(500)
def internal_error(error):
    db.session.rollback()
    return render_template('500.html'), 500

@app.errorhandler(Exception)
def handle_exception(e):
    # Log the error
    app.logger.error(f'Unhandled exception: {e}')
    return render_template('error.html'), 500


### Custom Error Pages
python
# 404 Error
@app.errorhandler(404)
def page_not_found(e):
    return render_template('404.html'), 404

# 403 Forbidden
@app.errorhandler(403)
def forbidden(e):
    return render_template('403.html'), 403

# 500 Internal Server Error
@app.errorhandler(500)
def internal_server_error(e):
    return render_template('500.html'), 500


---

## Configuration

### Configuration Methods

#### Method 1: Direct Assignment
python
app.config['DEBUG'] = True
app.config['TESTING'] = False
app.config['SECRET_KEY'] = 'your-secret-key'


#### Method 2: From Object
python
# config.py
class Config:
    SECRET_KEY = 'your-secret-key'
    SQLALCHEMY_DATABASE_URI = 'sqlite:///app.db'
    DEBUG = False

class DevelopmentConfig(Config):
    DEBUG = True

class ProductionConfig(Config):
    DEBUG = False

# app.py
app.config.from_object('config.DevelopmentConfig')


#### Method 3: From File
python
# config.cfg
DEBUG = True
SECRET_KEY = 'your-secret-key'
SQLALCHEMY_DATABASE_URI = 'sqlite:///app.db'

# app.py
app.config.from_pyfile('config.cfg')


#### Method 4: From Environment Variables
python
import os

app.config['SECRET_KEY'] = os.environ.get('SECRET_KEY', 'default-secret-key')
app.config['DATABASE_URI'] = os.environ.get('DATABASE_URI')


### Common Configuration Options
python
# General
DEBUG = True/False
TESTING = True/False
SECRET_KEY = 'your-secret-key'

# Database
SQLALCHEMY_DATABASE_URI = 'sqlite:///app.db'
SQLALCHEMY_TRACK_MODIFICATIONS = False

# Session
SESSION_COOKIE_NAME = 'session'
SESSION_COOKIE_SECURE = True
PERMANENT_SESSION_LIFETIME = timedelta(days=7)

# Upload
MAX_CONTENT_LENGTH = 16 * 1024 * 1024  # 16MB
UPLOAD_FOLDER = '/path/to/uploads'


---

## Testing

### Setup Testing
python
import pytest
from app import app, db

@pytest.fixture
def client():
    app.config['TESTING'] = True
    app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///:memory:'
    
    with app.test_client() as client:
        with app.app_context():
            db.create_all()
        yield client
        with app.app_context():
            db.drop_all()


### Writing Tests
python
def test_index(client):
    response = client.get('/')
    assert response.status_code == 200
    assert b'Hello' in response.data

def test_login(client):
    response = client.post('/login', data={
        'username': 'test',
        'password': 'password'
    })
    assert response.status_code == 302  # Redirect

def test_json_response(client):
    response = client.get('/api/data')
    assert response.status_code == 200
    assert response.json['status'] == 'success'


### Running Tests
bash
# Install pytest
pip install pytest pytest-cov

# Run tests
pytest

# Run with coverage
pytest --cov=app tests/


---

## Deployment

### Production Checklist
python
# Disable debug mode
DEBUG = False

# Use environment variables for secrets
SECRET_KEY = os.environ.get('SECRET_KEY')

# Use production database
SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URI')

# Enable HTTPS only cookies
SESSION_COOKIE_SECURE = True
SESSION_COOKIE_HTTPONLY = True

# Set proper logging
import logging
logging.basicConfig(level=logging.INFO)


### Using Gunicorn
bash
# Install gunicorn
pip install gunicorn

# Run with gunicorn
gunicorn -w 4 -b 0.0.0.0:8000 app:app

# With config file
gunicorn -c gunicorn_config.py app:app


### Gunicorn Config (gunicorn_config.py)
python
bind = "0.0.0.0:8000"
workers = 4
threads = 2
timeout = 120
accesslog = "access.log"
errorlog = "error.log"
loglevel = "info"


### Using Docker
dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:8000", "app:app"]


---

## Common Extensions

### Flask-SQLAlchemy
bash
pip install Flask-SQLAlchemy

python
from flask_sqlalchemy import SQLAlchemy
db = SQLAlchemy(app)


### Flask-Migrate
bash
pip install Flask-Migrate

python
from flask_migrate import Migrate
migrate = Migrate(app, db)

# Commands
flask db init
flask db migrate -m "Initial migration"
flask db upgrade


### Flask-Login
bash
pip install Flask-Login

python
from flask_login import LoginManager, UserMixin, login_user, logout_user, login_required

login_manager = LoginManager(app)
login_manager.login_view = 'login'

@login_manager.user_loader
def load_user(user_id):
    return User.query.get(int(user_id))

@app.route('/protected')
@login_required
def protected():
    return 'Protected page'


### Flask-CORS
bash
pip install Flask-CORS

python
from flask_cors import CORS
CORS(app)

# Or specific routes
CORS(app, resources={r"/api/*": {"origins": "*"}})


### Flask-Mail
bash
pip install Flask-Mail

python
from flask_mail import Mail, Message

app.config['MAIL_SERVER'] = 'smtp.gmail.com'
app.config['MAIL_PORT'] = 587
app.config['MAIL_USE_TLS'] = True
app.config['MAIL_USERNAME'] = 'your-email@gmail.com'
app.config['MAIL_PASSWORD'] = 'your-password'

mail = Mail(app)

@app.route('/send')
def send_email():
    msg = Message('Hello', sender='from@example.com', recipients=['to@example.com'])
    msg.body = 'This is the email body'
    mail.send(msg)
    return 'Email sent!'


### Flask-WTF
bash
pip install Flask-WTF

python
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField
from wtforms.validators import DataRequired

class MyForm(FlaskForm):
    name = StringField('Name', validators=[DataRequired()])
    submit = SubmitField('Submit')


---

## Blueprints (Modular Applications)

### Creating a Blueprint
python
# routes/auth.py
from flask import Blueprint, render_template, redirect, url_for

auth_bp = Blueprint('auth', __name__, url_prefix='/auth')

@auth_bp.route('/login')
def login():
    return render_template('login.html')

@auth_bp.route('/logout')
def logout():
    return redirect(url_for('index'))


### Registering Blueprints
python
# app.py
from routes.auth import auth_bp

app.register_blueprint(auth_bp)


---

## Useful Flask Commands

bash
# Set Flask app
export FLASK_APP=app.py

# Set environment
export FLASK_ENV=development

# Run development server
flask run

# Run on specific host/port
flask run --host=0.0.0.0 --port=8080

# Open Python shell with app context
flask shell

# Run database migrations
flask db init
flask db migrate
flask db upgrade

# Custom commands
flask custom-command


---

## Best Practices

1. **Always use virtual environments**
2. **Pin dependency versions** in requirements.txt
3. **Never commit secrets** to version control
4. **Use environment variables** for configuration
5. **Implement proper error handling**
6. **Use blueprints** for large applications
7. **Write tests** for your routes and functions
8. **Use Flask-Login** for authentication
9. **Validate all user input**
10. **Use HTTPS** in production
11. **Enable CSRF protection** with Flask-WTF
12. **Use proper logging** instead of print statements

---

## Quick Reference

### HTTP Status Codes
- 200: OK
- 201: Created
- 204: No Content
- 301: Moved Permanently
- 302: Found (Redirect)
- 400: Bad Request
- 401: Unauthorized
- 403: Forbidden
- 404: Not Found
- 500: Internal Server Error

### Request Methods
- GET: Retrieve data
- POST: Submit data
- PUT: Update data (full)
- PATCH: Update data (partial)
- DELETE: Delete data

---

*This cheat sheet covers Flask 2.2.2 and later versions. Always refer to the official Flask documentation for the most up-to-date information.*