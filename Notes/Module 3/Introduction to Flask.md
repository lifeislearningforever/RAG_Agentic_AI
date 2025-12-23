# Introduction to Flask

## Learning Objectives

After reading this guide, you will be able to:
- Define the Flask web framework and describe its main features
- Explain how to install Flask on your machines
- Describe main dependencies of Flask
- Explain the main differences between Flask and Django

---

## What is Flask?

Flask is a **micro framework** that can create web applications. It is not opinionated like some other larger frameworks and does not bind the user to a specific set of tools.

### Key Facts

- **Current Version:** Flask 2.2.2
- **Python Requirement:** Minimum Python version 3.7
- **Creator:** Armin Ronacher
- **Origin:** Created in 2004 as an April Fool's joke
- **Popularity:** Quickly gained popularity for its ease of use and extensibility

Flask provides minimal dependencies you might need to create a web application. However, it is extensible and many community extensions add additional features to Flask.

---

## Main Features of Flask

### Core Features

#### Development Web Server
Flask has a web server that runs applications in development mode.

#### Debugger
Flask comes with a debugger to help debug applications. The debugger shows interactive traceback and stack trace in the browser.

#### Logging
Flask uses standard Python logging for application logs, and you can use the same logger to log custom messages about your application.

#### Testing Support
Flask provides a way to test different parts of your application. The testing feature enables developers to follow a test-driven approach. You can use frameworks like pytest and coverage to ensure your code works as desired.

#### Request and Response Objects
Developers can access the request and response objects to pull arguments and customize responses.

### Additional Features

#### Static Assets
The framework supports static assets like CSS files, JavaScript files, and images. Flask provides tags to load static files in the templates.

#### Dynamic Pages with Jinja
You can develop dynamic pages using Jinja templating framework. These dynamic pages can display information that may change for each request or may check if the user is logged in.

#### Routing and Dynamic URLs
Flask provides routing and supports dynamic URLs that are extremely useful for RESTful services. You can create routes for different HTTP methods and provide redirection in your application.

#### Global Error Handlers
You can write global error handlers in Flask that work on the application level.

#### Session Management
Flask supports user session management.

---

## Popular Community Extensions

Flask can be extended with many community-built extensions:

### Database Extensions
- **Flask-SQLAlchemy:** Adds support for ORM called SQLAlchemy to Flask, giving developers a way to work with database objects in Python
- **Flask-Migrate:** Adds database migrations to SQLAlchemy ORM

### Communication Extensions
- **Flask-Mail:** Provides the ability to set up an SMTP mail server
- **Flask-CORS:** Allows your application to handle Cross-Origin Resource Sharing, making cross-origin JavaScript requests possible

### User Management
- **Flask-Admin:** Lets you add admin interfaces to Flask applications easily
- **Flask-User:** Adds user authentication, authorization, and other user management activities

### File and Data Handling
- **Flask-Uploads:** Allows you to add customized file uploading to your application
- **Marshmallow:** Adds extensive object serialization and deserialization support to your code

### Task Management
- **Celery:** A powerful task queue that can be used for simple background tasks and complex multi-stage programs and schedules

---

## Installing Flask

Flask is available on the Python package manager called **pip**.

### Installation Steps

1. **Create a Virtual Environment** (recommended)
   ```bash
   python -m venv myenv
   # or
   python -m bin venv myenv
   ```

2. **Install Flask**
   ```bash
   pip install Flask==2.2.2
   ```

### Version Pinning Best Practice

It is recommended to pin the version number of the dependencies in your application. This ensures:
- The application can be reproduced from scratch in different environments (development, staging, and production)
- New issues and bugs are not introduced by mistake when packages are updated automatically without a version number

### Viewing Installed Dependencies

To see the built-in dependencies, you can use the `pip freeze` command in the virtual environment, and you can see that all the built-in packages are installed by default.

---

## Built-in Dependencies

Flask comes with some built-in dependencies that enable the various features:

### Werkzeug
Implements WSGI (Web Server Gateway Interface). This is the standard Python interface between applications and servers.

### Jinja
A template language that renders the pages in your application.

### MarkupSafe
Comes with Jinja. It escapes untrusted input when rendering templates to avoid injection attacks.

### ItsDangerous
Used to assign data securely. This helps determine if data has been tampered with and is used to protect Flask session cookie.

### Click
A framework for writing command-line applications. It provides the Flask command and allows adding custom management commands.

---

## Flask vs. Django

Here are some key differences between Flask and Django:

| Feature | Flask | Django |
|---------|-------|--------|
| **Framework Type** | Micro framework (light) | Full-stack framework |
| **Dependencies** | Provides only basic dependencies needed to create a web application | Includes everything you need to create a full-stack application |
| **Flexibility** | Very flexible - you can add and remove pieces in a plug-and-play manner | Opinionated - makes most decisions for the developer |
| **Philosophy** | Developer chooses extensions that provide additional features | Developer can focus on the application's logic while framework handles structure |
| **Use Case** | Best for smaller applications or when you need fine control over components | Best for larger applications that benefit from built-in features |

---

## Summary

- Flask is a micro framework that ships with minimal dependencies
- To build websites, Flask has features like debugging servers, routing, templates, and error handling
- Flask can be extended by using community extensions
- Flask can be installed as a Python package
- Django is a full-stack framework compared to Flask
- Flask offers more flexibility while Django provides more built-in functionality

---

## Getting Started

To begin working with Flask:

1. Install Python 3.7 or higher
2. Create a virtual environment
3. Install Flask using pip
4. Start building your web application with minimal setup
5. Add extensions as needed for your specific use case

Flask's simplicity and extensibility make it an excellent choice for developers who want control over their application architecture while still having access to a robust ecosystem of extensions.