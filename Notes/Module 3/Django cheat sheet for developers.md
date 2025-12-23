# Django Web Development Cheat Sheet

## Table of Contents
- [Installation & Setup](#installation--setup)
- [Project Structure](#project-structure)
- [Django Management Commands](#django-management-commands)
- [Models](#models)
- [QuerySets & ORM](#querysets--orm)
- [Views](#views)
- [URLs & Routing](#urls--routing)
- [Templates](#templates)
- [Forms](#forms)
- [Admin Interface](#admin-interface)
- [Authentication](#authentication)
- [Middleware](#middleware)
- [Static & Media Files](#static--media-files)
- [REST API (Django REST Framework)](#rest-api-django-rest-framework)
- [Testing](#testing)
- [Deployment](#deployment)
- [Best Practices](#best-practices)

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


### Install Django
bash
# Install latest version
pip install Django

# Install specific version
pip install Django==4.2

# Save dependencies
pip freeze > requirements.txt

# Install from requirements
pip install -r requirements.txt


### Create New Project
bash
# Create project
django-admin startproject myproject

# Create project in current directory
django-admin startproject myproject .

# Create app
python manage.py startapp myapp


### Run Development Server
bash
# Default (localhost:8000)
python manage.py runserver

# Custom host and port
python manage.py runserver 0.0.0.0:8080

# Specific port
python manage.py runserver 8080


---

## Project Structure


myproject/
├── manage.py                 # Command-line utility
├── myproject/               # Project package
│   ├── __init__.py
│   ├── settings.py          # Project settings
│   ├── urls.py              # URL configuration
│   ├── asgi.py              # ASGI entry point
│   └── wsgi.py              # WSGI entry point
├── myapp/                   # Application package
│   ├── __init__.py
│   ├── admin.py             # Admin configuration
│   ├── apps.py              # App configuration
│   ├── models.py            # Data models
│   ├── views.py             # View functions/classes
│   ├── urls.py              # App URL patterns
│   ├── forms.py             # Form classes
│   ├── tests.py             # Test cases
│   ├── migrations/          # Database migrations
│   │   └── __init__.py
│   ├── templates/           # HTML templates
│   │   └── myapp/
│   │       └── index.html
│   └── static/              # Static files (CSS, JS, images)
│       └── myapp/
│           ├── css/
│           ├── js/
│           └── images/
├── static/                  # Project-wide static files
├── media/                   # User-uploaded files
├── templates/               # Project-wide templates
└── requirements.txt         # Project dependencies


---

## Django Management Commands

### Database Commands
bash
# Create migrations
python manage.py makemigrations

# Apply migrations
python manage.py migrate

# Show migrations
python manage.py showmigrations

# SQL for migration
python manage.py sqlmigrate myapp 0001

# Database shell
python manage.py dbshell


### User Management
bash
# Create superuser
python manage.py createsuperuser

# Change user password
python manage.py changepassword username


### Development
bash
# Run development server
python manage.py runserver

# Django shell
python manage.py shell

# Django shell with IPython
python manage.py shell -i ipython

# Check for issues
python manage.py check

# Show installed apps
python manage.py showmigrations


### Static Files
bash
# Collect static files
python manage.py collectstatic

# Find static files
python manage.py findstatic filename.css


### Testing
bash
# Run all tests
python manage.py test

# Run specific app tests
python manage.py test myapp

# Run specific test class
python manage.py test myapp.tests.MyTestClass

# Run with verbosity
python manage.py test --verbosity=2

# Keep test database
python manage.py test --keepdb


### Custom Commands
bash
# Create custom command directory
myapp/management/commands/mycommand.py

# Run custom command
python manage.py mycommand


---

## Models

### Basic Model Definition
python
from django.db import models
from django.contrib.auth.models import User

class Category(models.Model):
    name = models.CharField(max_length=100)
    slug = models.SlugField(unique=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        verbose_name_plural = 'Categories'
        ordering = ['name']
    
    def __str__(self):
        return self.name

class Post(models.Model):
    STATUS_CHOICES = [
        ('draft', 'Draft'),
        ('published', 'Published'),
    ]
    
    title = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='posts')
    category = models.ForeignKey(Category, on_delete=models.SET_NULL, null=True, blank=True)
    content = models.TextField()
    status = models.CharField(max_length=10, choices=STATUS_CHOICES, default='draft')
    featured_image = models.ImageField(upload_to='posts/%Y/%m/%d/', blank=True)
    views = models.PositiveIntegerField(default=0)
    published_at = models.DateTimeField(null=True, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['-published_at']
        indexes = [
            models.Index(fields=['-published_at']),
        ]
    
    def __str__(self):
        return self.title
    
    def get_absolute_url(self):
        from django.urls import reverse
        return reverse('post_detail', args=[self.slug])


### Field Types
python
# Text fields
models.CharField(max_length=200)
models.TextField()
models.SlugField()
models.EmailField()
models.URLField()

# Numeric fields
models.IntegerField()
models.PositiveIntegerField()
models.FloatField()
models.DecimalField(max_digits=10, decimal_places=2)

# Date/Time fields
models.DateField()
models.TimeField()
models.DateTimeField()
models.DurationField()

# Boolean
models.BooleanField(default=False)
models.NullBooleanField()

# File fields
models.FileField(upload_to='uploads/')
models.ImageField(upload_to='images/')

# Relationship fields
models.ForeignKey(OtherModel, on_delete=models.CASCADE)
models.ManyToManyField(OtherModel)
models.OneToOneField(OtherModel, on_delete=models.CASCADE)

# JSON field
models.JSONField()

# UUID field
models.UUIDField(default=uuid.uuid4, editable=False)


### Field Options
python
# Common field options
null=True                    # Database allows NULL
blank=True                   # Form validation allows empty
default='value'              # Default value
unique=True                  # Must be unique
db_index=True               # Create database index
choices=CHOICES             # Limit to specific values
help_text='Help text'       # Help text for forms
verbose_name='Name'         # Human-readable name
editable=False              # Not editable in forms
auto_now=True               # Update on every save
auto_now_add=True           # Set on creation only


### Model Relationships

#### ForeignKey (Many-to-One)
python
class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    content = models.TextField()

# on_delete options:
# CASCADE - Delete related objects
# PROTECT - Prevent deletion
# SET_NULL - Set to NULL (requires null=True)
# SET_DEFAULT - Set to default value
# SET() - Set to specific value
# DO_NOTHING - Do nothing (can cause database errors)


#### ManyToManyField
python
class Post(models.Model):
    tags = models.ManyToManyField('Tag', related_name='posts')

class Tag(models.Model):
    name = models.CharField(max_length=50)

# With through model (custom intermediate table)
class Post(models.Model):
    tags = models.ManyToManyField('Tag', through='PostTag')

class PostTag(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE)
    tag = models.ForeignKey(Tag, on_delete=models.CASCADE)
    added_at = models.DateTimeField(auto_now_add=True)


#### OneToOneField
python
class UserProfile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='profile')
    bio = models.TextField()
    avatar = models.ImageField(upload_to='avatars/')


### Model Methods
python
class Post(models.Model):
    # Fields...
    
    def __str__(self):
        """String representation"""
        return self.title
    
    def save(self, *args, **kwargs):
        """Override save method"""
        if not self.slug:
            self.slug = slugify(self.title)
        super().save(*args, **kwargs)
    
    def delete(self, *args, **kwargs):
        """Override delete method"""
        # Custom logic before deletion
        super().delete(*args, **kwargs)
    
    def get_absolute_url(self):
        """Get URL for object"""
        return reverse('post_detail', args=[self.slug])
    
    @property
    def is_published(self):
        """Custom property"""
        return self.status == 'published'
    
    @classmethod
    def published_posts(cls):
        """Class method"""
        return cls.objects.filter(status='published')


### Meta Options
python
class Post(models.Model):
    # Fields...
    
    class Meta:
        ordering = ['-created_at', 'title']
        verbose_name = 'Blog Post'
        verbose_name_plural = 'Blog Posts'
        db_table = 'blog_posts'
        unique_together = [['author', 'slug']]
        indexes = [
            models.Index(fields=['status', '-published_at']),
        ]
        permissions = [
            ('can_publish', 'Can publish posts'),
        ]
        get_latest_by = 'published_at'
        default_related_name = 'posts'


---

## QuerySets & ORM

### Basic Queries
python
from myapp.models import Post

# Get all objects
posts = Post.objects.all()

# Get single object
post = Post.objects.get(id=1)
post = Post.objects.get(slug='my-post')

# Get first/last
post = Post.objects.first()
post = Post.objects.last()

# Create object
post = Post.objects.create(title='Title', content='Content')

# Get or create
post, created = Post.objects.get_or_create(
    slug='my-slug',
    defaults={'title': 'Title', 'content': 'Content'}
)

# Update or create
post, created = Post.objects.update_or_create(
    slug='my-slug',
    defaults={'title': 'Updated Title'}
)

# Count
count = Post.objects.count()

# Exists
exists = Post.objects.filter(slug='my-post').exists()

# Delete
Post.objects.filter(status='draft').delete()
post.delete()


### Filtering
python
# Filter
posts = Post.objects.filter(status='published')
posts = Post.objects.filter(status='published', category__name='Tech')

# Exclude
posts = Post.objects.exclude(status='draft')

# Multiple conditions (AND)
posts = Post.objects.filter(status='published').filter(views__gt=100)

# OR conditions
from django.db.models import Q
posts = Post.objects.filter(Q(status='published') | Q(status='featured'))

# NOT condition
posts = Post.objects.filter(~Q(status='draft'))

# Complex queries
posts = Post.objects.filter(
    Q(status='published') & (Q(category__name='Tech') | Q(category__name='Science'))
)

# Field lookups
Post.objects.filter(title__exact='Exact Title')
Post.objects.filter(title__iexact='case insensitive')
Post.objects.filter(title__contains='word')
Post.objects.filter(title__icontains='Word')
Post.objects.filter(title__startswith='Start')
Post.objects.filter(title__endswith='End')
Post.objects.filter(views__gt=100)       # Greater than
Post.objects.filter(views__gte=100)      # Greater than or equal
Post.objects.filter(views__lt=100)       # Less than
Post.objects.filter(views__lte=100)      # Less than or equal
Post.objects.filter(published_at__year=2024)
Post.objects.filter(published_at__month=12)
Post.objects.filter(published_at__day=25)
Post.objects.filter(category__isnull=True)
Post.objects.filter(tags__in=['python', 'django'])
Post.objects.filter(title__regex=r'^(An?|The) +')


### Ordering
python
# Order by field
posts = Post.objects.order_by('title')
posts = Post.objects.order_by('-published_at')  # Descending

# Multiple fields
posts = Post.objects.order_by('-published_at', 'title')

# Random order
posts = Post.objects.order_by('?')

# Reverse
posts = Post.objects.order_by('-published_at').reverse()


### Slicing & Limiting
python
# First 5 posts
posts = Post.objects.all()[:5]

# Posts 5-10
posts = Post.objects.all()[5:10]

# Every other post
posts = Post.objects.all()[::2]

# Latest N objects
posts = Post.objects.order_by('-published_at')[:10]


### Select Related & Prefetch Related
python
# select_related (for ForeignKey and OneToOne)
# Reduces queries by doing SQL JOIN
posts = Post.objects.select_related('author', 'category').all()

# prefetch_related (for ManyToMany and reverse ForeignKey)
# Reduces queries by doing separate queries
posts = Post.objects.prefetch_related('tags', 'comments').all()

# Combining both
posts = Post.objects.select_related('author').prefetch_related('tags').all()

# Prefetch with custom queryset
from django.db.models import Prefetch
posts = Post.objects.prefetch_related(
    Prefetch('comments', queryset=Comment.objects.filter(approved=True))
).all()


### Aggregation
python
from django.db.models import Count, Sum, Avg, Max, Min

# Aggregate over entire queryset
Post.objects.aggregate(Avg('views'))
# Returns: {'views__avg': 123.45}

Post.objects.aggregate(
    total_views=Sum('views'),
    avg_views=Avg('views'),
    max_views=Max('views'),
    min_views=Min('views'),
    post_count=Count('id')
)

# Annotate (add field to each object)
posts = Post.objects.annotate(comment_count=Count('comments'))
# Access with: post.comment_count

# Filter by annotated field
posts = Post.objects.annotate(
    comment_count=Count('comments')
).filter(comment_count__gt=5)

# Group by
categories = Category.objects.annotate(
    post_count=Count('post')
).order_by('-post_count')


### F Expressions
python
from django.db.models import F

# Reference field values
Post.objects.filter(views__gt=F('likes'))

# Update with F expression (atomic)
Post.objects.update(views=F('views') + 1)

# Math operations
Post.objects.annotate(total=F('price') * F('quantity'))


### Q Objects (Complex Lookups)
python
from django.db.models import Q

# OR
posts = Post.objects.filter(Q(status='published') | Q(status='featured'))

# AND
posts = Post.objects.filter(Q(status='published') & Q(views__gt=100))

# NOT
posts = Post.objects.filter(~Q(status='draft'))

# Complex
posts = Post.objects.filter(
    Q(status='published'),
    Q(category__name='Tech') | Q(category__name='Science'),
    ~Q(author__username='banned_user')
)


### Bulk Operations
python
# Bulk create
posts = [
    Post(title='Post 1', content='Content 1'),
    Post(title='Post 2', content='Content 2'),
]
Post.objects.bulk_create(posts)

# Bulk update
posts = Post.objects.filter(category__name='Tech')
for post in posts:
    post.status = 'published'
Post.objects.bulk_update(posts, ['status'])

# Update (single query)
Post.objects.filter(status='draft').update(status='published')


### Raw SQL
python
# Raw queries
posts = Post.objects.raw('SELECT * FROM myapp_post WHERE status = %s', ['published'])

# Execute custom SQL
from django.db import connection
with connection.cursor() as cursor:
    cursor.execute("UPDATE myapp_post SET views = views + 1 WHERE id = %s", [post_id])
    cursor.execute("SELECT * FROM myapp_post")
    rows = cursor.fetchall()


---

## Views

### Function-Based Views (FBV)
python
from django.shortcuts import render, get_object_or_404, redirect
from django.http import HttpResponse, JsonResponse
from .models import Post
from .forms import PostForm

# Simple view
def index(request):
    return HttpResponse("Hello, World!")

# Render template
def post_list(request):
    posts = Post.objects.filter(status='published')
    return render(request, 'myapp/post_list.html', {'posts': posts})

# Detail view
def post_detail(request, slug):
    post = get_object_or_404(Post, slug=slug)
    return render(request, 'myapp/post_detail.html', {'post': post})

# Form handling
def post_create(request):
    if request.method == 'POST':
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            post = form.save(commit=False)
            post.author = request.user
            post.save()
            return redirect('post_detail', slug=post.slug)
    else:
        form = PostForm()
    return render(request, 'myapp/post_form.html', {'form': form})

# JSON response
def api_posts(request):
    posts = Post.objects.values('id', 'title', 'slug')
    return JsonResponse(list(posts), safe=False)

# Require methods
from django.views.decorators.http import require_http_methods

@require_http_methods(["GET", "POST"])
def my_view(request):
    pass

# Login required
from django.contrib.auth.decorators import login_required

@login_required
def protected_view(request):
    return render(request, 'myapp/protected.html')

# Permission required
from django.contrib.auth.decorators import permission_required

@permission_required('myapp.can_publish')
def publish_post(request, post_id):
    pass


### Class-Based Views (CBV)
python
from django.views import View
from django.views.generic import (
    ListView, DetailView, CreateView, UpdateView, DeleteView, TemplateView
)
from django.urls import reverse_lazy
from django.contrib.auth.mixins import LoginRequiredMixin, PermissionRequiredMixin

# Template view
class HomeView(TemplateView):
    template_name = 'myapp/home.html'
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['latest_posts'] = Post.objects.order_by('-published_at')[:5]
        return context

# List view
class PostListView(ListView):
    model = Post
    template_name = 'myapp/post_list.html'
    context_object_name = 'posts'
    paginate_by = 10
    
    def get_queryset(self):
        return Post.objects.filter(status='published')
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['categories'] = Category.objects.all()
        return context

# Detail view
class PostDetailView(DetailView):
    model = Post
    template_name = 'myapp/post_detail.html'
    context_object_name = 'post'
    slug_field = 'slug'
    
    def get_queryset(self):
        return Post.objects.select_related('author', 'category')

# Create view
class PostCreateView(LoginRequiredMixin, CreateView):
    model = Post
    form_class = PostForm
    template_name = 'myapp/post_form.html'
    success_url = reverse_lazy('post_list')
    
    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)

# Update view
class PostUpdateView(LoginRequiredMixin, UpdateView):
    model = Post
    form_class = PostForm
    template_name = 'myapp/post_form.html'
    
    def get_queryset(self):
        # Only allow users to edit their own posts
        return Post.objects.filter(author=self.request.user)

# Delete view
class PostDeleteView(LoginRequiredMixin, DeleteView):
    model = Post
    template_name = 'myapp/post_confirm_delete.html'
    success_url = reverse_lazy('post_list')
    
    def get_queryset(self):
        return Post.objects.filter(author=self.request.user)

# Generic View
class MyView(View):
    def get(self, request):
        return render(request, 'myapp/template.html')
    
    def post(self, request):
        # Handle POST
        return redirect('success')


---

## URLs & Routing

### URL Configuration
python
# myproject/urls.py
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('myapp.urls')),
    path('blog/', include('blog.urls')),
    path('api/', include('api.urls')),
]

# Serve media files in development
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

# myapp/urls.py
from django.urls import path, re_path
from . import views

app_name = 'myapp'  # Namespace

urlpatterns = [
    # Function-based views
    path('', views.index, name='index'),
    path('about/', views.about, name='about'),
    path('posts/', views.post_list, name='post_list'),
    path('posts/<slug:slug>/', views.post_detail, name='post_detail'),
    path('posts/<int:id>/', views.post_by_id, name='post_by_id'),
    
    # Class-based views
    path('posts/create/', views.PostCreateView.as_view(), name='post_create'),
    path('posts/<slug:slug>/edit/', views.PostUpdateView.as_view(), name='post_update'),
    path('posts/<slug:slug>/delete/', views.PostDeleteView.as_view(), name='post_delete'),
    
    # Regex patterns
    re_path(r'^archive/(?P<year>[0-9]{4})/$', views.year_archive, name='year_archive'),
]


### URL Patterns
python
# Path converters
path('posts/<int:id>/', ...)          # Integer
path('posts/<str:username>/', ...)    # String
path('posts/<slug:slug>/', ...)       # Slug (letters, numbers, hyphens, underscores)
path('posts/<uuid:uuid>/', ...)       # UUID
path('files/<path:file_path>/', ...)  # Path (includes slashes)

# Regular expressions
from django.urls import re_path
re_path(r'^posts/(?P<year>[0-9]{4})/$', ...)
re_path(r'^posts/(?P<slug>[\w-]+)/$', ...)


### URL Reversing
python
from django.urls import reverse
from django.shortcuts import redirect

# In views
url = reverse('myapp:post_detail', args=[post.slug])
url = reverse('myapp:post_detail', kwargs={'slug': post.slug})
return redirect('myapp:post_list')

# In templates
{% url 'myapp:post_detail' post.slug %}
{% url 'myapp:post_detail' slug=post.slug %}

# In models
class Post(models.Model):
    # ...
    def get_absolute_url(self):
        return reverse('myapp:post_detail', args=[self.slug])


---

## Templates

### Template Syntax
django
{# This is a comment #}

{# Variables #}
{{ variable }}
{{ object.attribute }}
{{ dictionary.key }}
{{ list.0 }}
{{ object.method }}

{# Filters #}
{{ text|lower }}
{{ text|upper }}
{{ text|title }}
{{ text|truncatewords:30 }}
{{ date|date:"Y-m-d" }}
{{ value|default:"Nothing" }}
{{ html|safe }}
{{ number|floatformat:2 }}
{{ list|join:", " }}
{{ text|length }}
{{ value|add:5 }}

{# Tags #}
{% if condition %}
    <p>True</p>
{% elif other_condition %}
    <p>Other</p>
{% else %}
    <p>False</p>
{% endif %}

{% for item in items %}
    <li>{{ forloop.counter }}: {{ item }}</li>
{% empty %}
    <li>No items</li>
{% endfor %}

{# For loop variables #}
{{ forloop.counter }}      {# 1-indexed #}
{{ forloop.counter0 }}     {# 0-indexed #}
{{ forloop.revcounter }}   {# Reverse counter #}
{{ forloop.first }}        {# First iteration #}
{{ forloop.last }}         {# Last iteration #}
{{ forloop.parentloop }}   {# Parent loop #}

{# URL tag #}
<a href="{% url 'myapp:post_detail' post.slug %}">{{ post.title }}</a>

{# Static files #}
{% load static %}
<link rel="stylesheet" href="{% static 'myapp/css/style.css' %}">
<img src="{% static 'myapp/images/logo.png' %}" alt="Logo">

{# Include #}
{% include 'myapp/sidebar.html' %}
{% include 'myapp/sidebar.html' with title="Custom Title" %}

{# Block and extends #}
{% extends 'base.html' %}

{% block content %}
    <h1>Page Content</h1>
{% endblock %}

{# CSRF token (required for POST forms) #}
<form method="post">
    {% csrf_token %}
    <!-- form fields -->
</form>

{# With #}
{% with total=business.employees.count %}
    {{ total }} employee{{ total|pluralize }}
{% endwith %}

{# Now #}
{% now "Y-m-d H:i" %}

{# Spaceless #}
{% spaceless %}
    <p>
        <a href="#">Link</a>
    </p>
{% endspaceless %}


### Template Inheritance
django
{# base.html #}
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}My Site{% endblock %}</title>
    {% block extra_css %}{% endblock %}
</head>
<body>
    <nav>
        {% block navigation %}
        <ul>
            <li><a href="{% url 'home' %}">Home</a></li>
            <li><a href="{% url 'about' %}">About</a></li>
        </ul>
        {% endblock %}
    </nav>
    
    <main>
        {% block content %}{% endblock %}
    </main>
    
    <footer>
        {% block footer %}
        <p>&copy; 2024 My Site</p>
        {% endblock %}
    </footer>
    
    {% block extra_js %}{% endblock %}
</body>
</html>

{# page.html #}
{% extends 'base.html' %}

{% block title %}{{ post.title }} - {{ block.super }}{% endblock %}

{% block content %}
    <h1>{{ post.title }}</h1>
    <p>{{ post.content }}</p>
{% endblock %}

{% block extra_css %}
    <link rel="stylesheet" href="{% static 'myapp/css/post.css' %}">
{% endblock %}


### Custom Template Tags & Filters
python
# myapp/templatetags/myapp_tags.py
from django import template

register = template.Library()

# Simple tag
@register.simple_tag
def current_time(format_string):
    return datetime.now().strftime(format_string)

# Inclusion tag
@register.inclusion_tag('myapp/latest_posts.html')
def show_latest_posts(count=5):
    posts = Post.objects.order_by('-published_at')[:count]
    return {'posts': posts}

# Filter
@register.filter
def multiply(value, arg):
    return value * arg

# Usage in templates
{% load myapp_tags %}
{% current_time "%Y-%m-%d %H:%M" %}
{% show_latest_posts 10 %}
{{ value|multiply:5 }}


---

## Forms

### Form Definition
python
from django import forms
from .models import Post

# Regular form
class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)
    subscribe = forms.BooleanField(required=False)
    
    def clean_email(self):
        email = self.cleaned_data.get('email')
        if not email.endswith('@example.com'):
            raise forms.ValidationError('Email must be from example.com')
        return email
    
    def clean(self):
        cleaned_data = super().clean()
        # Cross-field validation
        return cleaned_data

# ModelForm
class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'slug', 'category', 'content', 'status', 'featured_image']
        # Or exclude specific fields
        # exclude = ['author', 'views']
        # Or all fields
        # fields = '__all__'
        
        widgets = {
            'content': forms.Textarea(attrs={'rows': 10, 'class': 'form-control'}),
            'status': forms.Select(attrs={'class': 'form-select'}),
        }
        
        labels = {
            'content': 'Post Content',
        }
        
        help_texts = {
            'slug': 'URL-friendly version of the title',
        }
    
    def clean_slug(self):
        slug = self.cleaned_data.get('slug')
        if Post.objects.filter(slug=slug).exists():
            raise forms.ValidationError('This slug is already in use')
        return slug


### Form Fields
python
# Text fields
forms.CharField(max_length=100)
forms.EmailField()
forms.URLField()
forms.SlugField()
forms.RegexField(regex=r'^\d{3}-\d{3}-\d{4}$')

# Choice fields
forms.ChoiceField(choices=CHOICES)
forms.MultipleChoiceField(choices=CHOICES)
forms.TypedChoiceField(choices=CHOICES, coerce=int)
forms.ModelChoiceField(queryset=Category.objects.all())
forms.ModelMultipleChoiceField(queryset=Category.objects.all())

# Boolean
forms.BooleanField(required=False)
forms.NullBooleanField()

# Numeric
forms.IntegerField()
forms.FloatField()
forms.DecimalField(max_digits=10, decimal_places=2)

# Date/Time
forms.DateField()
forms.TimeField()
forms.DateTimeField()
forms.DurationField()

# File fields
forms.FileField()
forms.ImageField()

# Other
forms.GenericIPAddressField()
forms.UUIDField()
forms.JSONField()


### Form Widgets
python
from django import forms

# Text widgets
forms.TextInput(attrs={'class': 'form-control', 'placeholder': 'Enter text'})
forms.Textarea(attrs={'rows': 5, 'cols': 40})
forms.PasswordInput()
forms.HiddenInput()

# Choice widgets
forms.Select()
forms.RadioSelect()
forms.CheckboxInput()
forms.CheckboxSelectMultiple()
forms.SelectMultiple()

# Date/Time widgets
forms.DateInput(attrs={'type': 'date'})
forms.DateTimeInput(attrs={'type': 'datetime-local'})
forms.TimeInput(attrs={'type': 'time'})

# File widgets
forms.FileInput()
forms.ClearableFileInput()


### Using Forms in Views
python
# Function-based view
def contact(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            # Access cleaned data
            name = form.cleaned_data['name']
            email = form.cleaned_data['email']
            message = form.cleaned_data['message']
            # Process form
            return redirect('success')
    else:
        form = ContactForm()
    return render(request, 'contact.html', {'form': form})

# With ModelForm
def create_post(request):
    if request.method == 'POST':
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            post = form.save(commit=False)
            post.author = request.user
            post.save()
            form.save_m2m()  # Save many-to-many if commit=False
            return redirect('post_detail', slug=post.slug)
    else:
        form = PostForm()
    return render(request, 'post_form.html', {'form': form})

# Update with ModelForm
def update_post(request, slug):
    post = get_object_or_404(Post, slug=slug)
    if request.method == 'POST':
        form = PostForm(request.POST, request.FILES, instance=post)
        if form.is_valid():
            form.save()
            return redirect('post_detail', slug=post.slug)
    else:
        form = PostForm(instance=post)
    return render(request, 'post_form.html', {'form': form, 'post': post})


### Rendering Forms in Templates
django
{# Render entire form #}
<form method="post" enctype="multipart/form-data">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Submit</button>
</form>

{# As table #}
<form method="post">
    {% csrf_token %}
    <table>
    {{ form.as_table }}
    </table>
    <button type="submit">Submit</button>
</form>

{# As ul #}
<form method="post">
    {% csrf_token %}
    <ul>
    {{ form.as_ul }}
    </ul>
    <button type="submit">Submit</button>
</form>

{# Manual field rendering #}
<form method="post">
    {% csrf_token %}
    
    {% if form.errors %}
        <div class="alert alert-danger">
            {{ form.errors }}
        </div>
    {% endif %}
    
    <div class="form-group">
        {{ form.name.label_tag }}
        {{ form.name }}
        {% if form.name.errors %}
            <div class="error">{{ form.name.errors }}</div>
        {% endif %}
        {% if form.name.help_text %}
            <small>{{ form.name.help_text }}</small>
        {% endif %}
    </div>
    
    <button type="submit">Submit</button>
</form>

{# With Bootstrap classes #}
{% for field in form %}
<div class="mb-3">
    {{ field.label_tag }}
    {{ field|add_class:"form-control" }}
    {% if field.help_text %}
        <small class="form-text text-muted">{{ field.help_text }}</small>
    {% endif %}
    {% if field.errors %}
        <div class="invalid-feedback d-block">{{ field.errors }}</div>
    {% endif %}
</div>
{% endfor %}


---

## Admin Interface

### Basic Admin Registration
python
from django.contrib import admin
from .models import Post, Category

# Simple registration
admin.site.register(Post)

# With ModelAdmin
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'author', 'status', 'published_at', 'views']
    list_filter = ['status', 'category', 'published_at']
    search_fields = ['title', 'content']
    prepopulated_fields = {'slug': ('title',)}
    date_hierarchy = 'published_at'
    ordering = ['-published_at']
    readonly_fields = ['views', 'created_at', 'updated_at']
    
    fieldsets = (
        ('Basic Information', {
            'fields': ('title', 'slug', 'author', 'category')
        }),
        ('Content', {
            'fields': ('content', 'featured_image')
        }),
        ('Status', {
            'fields': ('status', 'published_at')
        }),
        ('Metadata', {
            'fields': ('views', 'created_at', 'updated_at'),
            'classes': ('collapse',)
        }),
    )
    
    # Custom actions
    actions = ['make_published']
    
    def make_published(self, request, queryset):
        queryset.update(status='published')
    make_published.short_description = "Mark selected posts as published"
    
    # Custom methods in list_display
    def author_email(self, obj):
        return obj.author.email
    author_email.short_description = 'Author Email'

# Inline admin
class CommentInline(admin.TabularInline):
    model = Comment
    extra = 1
    fields = ['author', 'content', 'approved']

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    inlines = [CommentInline]


### Admin Customization
python
# admin.py
from django.contrib import admin

# Customize admin site
admin.site.site_header = "My Site Administration"
admin.site.site_title = "My Site Admin Portal"
admin.site.index_title = "Welcome to My Site Admin"

# Custom admin actions
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    actions = ['make_published', 'make_draft']
    
    def make_published(self, request, queryset):
        count = queryset.update(status='published')
        self.message_user(request, f'{count} posts were marked as published.')
    make_published.short_description = "Publish selected posts"
    
    def make_draft(self, request, queryset):
        count = queryset.update(status='draft')
        self.message_user(request, f'{count} posts were marked as draft.')
    make_draft.short_description = "Mark selected as draft"
    
    # Override queryset
    def get_queryset(self, request):
        qs = super().get_queryset(request)
        if request.user.is_superuser:
            return qs
        return qs.filter(author=request.user)
    
    # Override save
    def save_model(self, request, obj, form, change):
        if not change:  # New object
            obj.author = request.user
        super().save_model(request, obj, form, change)


---

## Authentication

### User Model
python
from django.contrib.auth.models import User

# Create user
user = User.objects.create_user('john', 'john@example.com', 'password')
user.first_name = 'John'
user.last_name = 'Doe'
user.save()

# Create superuser
User.objects.create_superuser('admin', 'admin@example.com', 'password')

# Authenticate
from django.contrib.auth import authenticate, login, logout

user = authenticate(username='john', password='password')
if user is not None:
    login(request, user)

# Logout
logout(request)

# Check authentication
if request.user.is_authenticated:
    # User is logged in
    pass

# Check permissions
if request.user.has_perm('myapp.add_post'):
    # User has permission
    pass

# Check groups
if request.user.groups.filter(name='Editors').exists():
    # User is in Editors group
    pass


### Custom User Model
python
# models.py
from django.contrib.auth.models import AbstractUser

class CustomUser(AbstractUser):
    bio = models.TextField(blank=True)
    birth_date = models.DateField(null=True, blank=True)
    avatar = models.ImageField(upload_to='avatars/', blank=True)

# settings.py
AUTH_USER_MODEL = 'myapp.CustomUser'


### Login/Logout Views
python
# views.py
from django.contrib.auth import authenticate, login, logout
from django.contrib.auth.decorators import login_required
from django.shortcuts import render, redirect

def login_view(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        user = authenticate(request, username=username, password=password)
        if user is not None:
            login(request, user)
            return redirect('dashboard')
        else:
            return render(request, 'login.html', {'error': 'Invalid credentials'})
    return render(request, 'login.html')

def logout_view(request):
    logout(request)
    return redirect('home')

@login_required
def profile(request):
    return render(request, 'profile.html')

# Using built-in views (urls.py)
from django.contrib.auth import views as auth_views

urlpatterns = [
    path('login/', auth_views.LoginView.as_view(template_name='login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(), name='logout'),
    path('password-reset/', auth_views.PasswordResetView.as_view(), name='password_reset'),
]


### Permission Decorators
python
from django.contrib.auth.decorators import login_required, permission_required, user_passes_test

@login_required(login_url='/login/')
def protected_view(request):
    pass

@permission_required('myapp.add_post', raise_exception=True)
def create_post(request):
    pass

def is_staff(user):
    return user.is_staff

@user_passes_test(is_staff)
def staff_only(request):
    pass

# For class-based views
from django.contrib.auth.mixins import LoginRequiredMixin, PermissionRequiredMixin

class ProtectedView(LoginRequiredMixin, View):
    login_url = '/login/'
    redirect_field_name = 'redirect_to'
    
class CreatePostView(PermissionRequiredMixin, CreateView):
    permission_required = 'myapp.add_post'


---

## Middleware

### Built-in Middleware
python
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]


### Custom Middleware
python
# myapp/middleware.py

# Function-based middleware
def simple_middleware(get_response):
    # One-time configuration and initialization
    
    def middleware(request):
        # Code executed before view
        
        response = get_response(request)
        
        # Code executed after view
        
        return response
    
    return middleware

# Class-based middleware
class CustomMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        # One-time configuration
    
    def __call__(self, request):
        # Code executed before view
        
        response = self.get_response(request)
        
        # Code executed after view
        
        return response
    
    def process_view(self, request, view_func, view_args, view_kwargs):
        # Called before Django calls the view
        pass
    
    def process_exception(self, request, exception):
        # Called when view raises an exception
        pass
    
    def process_template_response(self, request, response):
        # Called after view returns TemplateResponse
        return response

# Example: Request logging middleware
import logging

logger = logging.getLogger(__name__)

class RequestLoggingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        # Log request
        logger.info(f'{request.method} {request.path}')
        
        response = self.get_response(request)
        
        # Log response
        logger.info(f'Response: {response.status_code}')
        
        return response

# Add to settings.py
MIDDLEWARE = [
    # ...
    'myapp.middleware.RequestLoggingMiddleware',
]


---

## Static & Media Files

### Settings Configuration
python
# settings.py
import os

# Static files (CSS, JavaScript, Images)
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]

# Media files (User uploads)
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')


### URL Configuration
python
# urls.py
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ... your url patterns
]

# Serve media files in development
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)


### Using in Templates
django
{% load static %}

<!-- Static files -->
<link rel="stylesheet" href="{% static 'css/style.css' %}">
<script src="{% static 'js/script.js' %}"></script>
<img src="{% static 'images/logo.png' %}" alt="Logo">

<!-- Media files -->
<img src="{{ post.featured_image.url }}" alt="{{ post.title }}">
<a href="{{ document.file.url }}">Download</a>


### Collecting Static Files
bash
# Collect all static files to STATIC_ROOT
python manage.py collectstatic

# Collect without prompting
python manage.py collectstatic --noinput

# Clear existing files before collecting
python manage.py collectstatic --clear


---

## REST API (Django REST Framework)

### Installation
bash
pip install djangorestframework


### Settings
python
# settings.py
INSTALLED_APPS = [
    # ...
    'rest_framework',
]

REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticatedOrReadOnly',
    ],
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10
}


### Serializers
python
# serializers.py
from rest_framework import serializers
from .models import Post, Category

class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ['id', 'name', 'slug']

class PostSerializer(serializers.ModelSerializer):
    author = serializers.StringRelatedField(read_only=True)
    category = CategorySerializer(read_only=True)
    
    class Meta:
        model = Post
        fields = ['id', 'title', 'slug', 'author', 'category', 'content', 
                  'status', 'views', 'published_at', 'created_at']
        read_only_fields = ['views', 'created_at']
    
    def validate_title(self, value):
        if len(value) < 5:
            raise serializers.ValidationError("Title must be at least 5 characters")
        return value

# Nested serializer
class PostDetailSerializer(serializers.ModelSerializer):
    author = UserSerializer(read_only=True)
    category = CategorySerializer(read_only=True)
    comments = CommentSerializer(many=True, read_only=True)
    comment_count = serializers.SerializerMethodField()
    
    class Meta:
        model = Post
        fields = '__all__'
    
    def get_comment_count(self, obj):
        return obj.comments.count()


### Views
python
# views.py
from rest_framework import viewsets, status, generics
from rest_framework.decorators import api_view, action
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticatedOrReadOnly, IsAuthenticated
from .models import Post
from .serializers import PostSerializer

# Function-based API view
@api_view(['GET', 'POST'])
def post_list(request):
    if request.method == 'GET':
        posts = Post.objects.all()
        serializer = PostSerializer(posts, many=True)
        return Response(serializer.data)
    
    elif request.method == 'POST':
        serializer = PostSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save(author=request.user)
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

# Generic views
class PostListView(generics.ListCreateAPIView):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]
    
    def perform_create(self, serializer):
        serializer.save(author=self.request.user)

class PostDetailView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    lookup_field = 'slug'

# ViewSets
class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]
    lookup_field = 'slug'
    
    def get_queryset(self):
        queryset = Post.objects.all()
        status = self.request.query_params.get('status')
        if status:
            queryset = queryset.filter(status=status)
        return queryset
    
    def perform_create(self, serializer):
        serializer.save(author=self.request.user)
    
    # Custom action
    @action(detail=True, methods=['post'])
    def publish(self, request, slug=None):
        post = self.get_object()
        post.status = 'published'
        post.save()
        serializer = self.get_serializer(post)
        return Response(serializer.data)
    
    @action(detail=False, methods=['get'])
    def recent(self, request):
        recent_posts = Post.objects.order_by('-published_at')[:5]
        serializer = self.get_serializer(recent_posts, many=True)
        return Response(serializer.data)


### URLs
python
# urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from . import views

# With ViewSets
router = DefaultRouter()
router.register(r'posts', views.PostViewSet)

urlpatterns = [
    path('api/', include(router.urls)),
]

# Or with generic views
urlpatterns = [
    path('api/posts/', views.PostListView.as_view()),
    path('api/posts/<slug:slug>/', views.PostDetailView.as_view()),
]


### Authentication
python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    ],
}

INSTALLED_APPS = [
    # ...
    'rest_framework.authtoken',
]

# urls.py
from rest_framework.authtoken.views import obtain_auth_token

urlpatterns = [
    path('api-token-auth/', obtain_auth_token),
]

# Obtain token
# POST to /api-token-auth/ with username and password
# Returns: {"token": "9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b"}

# Use token in requests
# Header: Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b


---

## Testing

### Test Classes
python
# tests.py
from django.test import TestCase, Client
from django.contrib.auth.models import User
from django.urls import reverse
from .models import Post

class PostModelTest(TestCase):
    @classmethod
    def setUpTestData(cls):
        # Set up data for the whole TestCase
        User.objects.create_user(username='testuser', password='12345')
    
    def setUp(self):
        # Set up before each test method
        self.user = User.objects.get(username='testuser')
        self.post = Post.objects.create(
            title='Test Post',
            slug='test-post',
            author=self.user,
            content='Test content'
        )
    
    def test_post_creation(self):
        self.assertEqual(self.post.title, 'Test Post')
        self.assertEqual(self.post.slug, 'test-post')
        self.assertEqual(str(self.post), 'Test Post')
    
    def test_post_absolute_url(self):
        self.assertEqual(self.post.get_absolute_url(), '/posts/test-post/')

class PostViewTest(TestCase):
    def setUp(self):
        self.client = Client()
        self.user = User.objects.create_user(username='testuser', password='12345')
        self.post = Post.objects.create(
            title='Test Post',
            slug='test-post',
            author=self.user,
            content='Test content',
            status='published'
        )
    
    def test_post_list_view(self):
        response = self.client.get(reverse('post_list'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'Test Post')
        self.assertTemplateUsed(response, 'myapp/post_list.html')
    
    def test_post_detail_view(self):
        response = self.client.get(reverse('post_detail', args=['test-post']))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'Test content')
    
    def test_post_create_requires_login(self):
        response = self.client.get(reverse('post_create'))
        self.assertEqual(response.status_code, 302)  # Redirect to login
    
    def test_post_create_authenticated(self):
        self.client.login(username='testuser', password='12345')
        response = self.client.post(reverse('post_create'), {
            'title': 'New Post',
            'slug': 'new-post',
            'content': 'New content',
            'status': 'draft'
        })
        self.assertEqual(response.status_code, 302)
        self.assertTrue(Post.objects.filter(slug='new-post').exists())

class PostAPITest(TestCase):
    def setUp(self):
        self.client = Client()
        self.user = User.objects.create_user(username='testuser', password='12345')
        self.post = Post.objects.create(
            title='Test Post',
            slug='test-post',
            author=self.user,
            content='Test content'
        )
    
    def test_api_post_list(self):
        response = self.client.get('/api/posts/')
        self.assertEqual(response.status_code, 200)
        self.assertEqual(len(response.json()), 1)
    
    def test_api_post_detail(self):
        response = self.client.get(f'/api/posts/{self.post.slug}/')
        self.assertEqual(response.status_code, 200)
        self.assertEqual(response.json()['title'], 'Test Post')


### Running Tests
bash
# Run all tests
python manage.py test

# Run specific app tests
python manage.py test myapp

# Run specific test class
python manage.py test myapp.tests.PostModelTest

# Run specific test method
python manage.py test myapp.tests.PostModelTest.test_post_creation

# With verbosity
python manage.py test --verbosity=2

# Keep test database
python manage.py test --keepdb

# Run tests in parallel
python manage.py test --parallel

# With coverage
coverage run --source='.' manage.py test
coverage report
coverage html


### Test Client Methods
python
# GET request
response = self.client.get('/path/')
response = self.client.get(reverse('view_name'))

# POST request
response = self.client.post('/path/', {'key': 'value'})
response = self.client.post(reverse('view_name'), data={'key': 'value'})

# With files
with open('test.jpg', 'rb') as fp:
    response = self.client.post('/upload/', {'file': fp})

# Follow redirects
response = self.client.get('/path/', follow=True)

# Login
self.client.login(username='user', password='pass')

# Force login (skip authentication)
self.client.force_login(self.user)

# Logout
self.client.logout()

# Assertions
self.assertEqual(response.status_code, 200)
self.assertContains(response, 'text')
self.assertNotContains(response, 'text')
self.assertTemplateUsed(response, 'template.html')
self.assertRedirects(response, '/path/')
self.assertJSONEqual(response.content, {'key': 'value'})


---

## Deployment

### Production Settings
python
# settings.py

# Security
DEBUG = False
ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com']
SECRET_KEY = os.environ.get('SECRET_KEY')

# Database
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.environ.get('DB_NAME'),
        'USER': os.environ.get('DB_USER'),
        'PASSWORD': os.environ.get('DB_PASSWORD'),
        'HOST': os.environ.get('DB_HOST'),
        'PORT': os.environ.get('DB_PORT', '5432'),
    }
}

# Static files
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
STATICFILES_STORAGE = 'django.contrib.staticfiles.storage.ManifestStaticFilesStorage'

# Security settings
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
SECURE_BROWSER_XSS_FILTER = True
SECURE_CONTENT_TYPE_NOSNIFF = True
X_FRAME_OPTIONS = 'DENY'

# HTTPS
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')

# HSTS
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True


### Using Environment Variables
python
# Install python-decouple
# pip install python-decouple

from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': config('DB_NAME'),
        'USER': config('DB_USER'),
        'PASSWORD': config('DB_PASSWORD'),
        'HOST': config('DB_HOST'),
        'PORT': config('DB_PORT', default='5432'),
    }
}

# .env file
SECRET_KEY=your-secret-key-here
DEBUG=False
DB_NAME=mydb
DB_USER=myuser
DB_PASSWORD=mypassword
DB_HOST=localhost
DB_PORT=5432


### Gunicorn
bash
# Install gunicorn
pip install gunicorn

# Run with gunicorn
gunicorn myproject.wsgi:application --bind 0.0.0.0:8000

# With workers
gunicorn myproject.wsgi:application --bind 0.0.0.0:8000 --workers 4

# With config file
gunicorn myproject.wsgi:application -c gunicorn_config.py

# gunicorn_config.py
bind = "0.0.0.0:8000"
workers = 4
threads = 2
worker_class = "sync"
timeout = 120
accesslog = "access.log"
errorlog = "error.log"
loglevel = "info"


### Docker
dockerfile
# Dockerfile
FROM python:3.11-slim

ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN python manage.py collectstatic --noinput

EXPOSE 8000

CMD ["gunicorn", "myproject.wsgi:application", "--bind", "0.0.0.0:8000", "--workers", "4"]


yaml
# docker-compose.yml
version: '3.8'

services:
  db:
    image: postgres:15
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
    volumes:
      - postgres_data:/var/lib/postgresql/data

  web:
    build: .
    command: gunicorn myproject.wsgi:application --bind 0.0.0.0:8000
    volumes:
      - .:/app
      - static_volume:/app/staticfiles
      - media_volume:/app/media
    ports:
      - "8000:8000"
    env_file:
      - .env
    depends_on:
      - db

  nginx:
    image: nginx:latest
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - static_volume:/app/staticfiles
      - media_volume:/app/media
    ports:
      - "80:80"
    depends_on:
      - web

volumes:
  postgres_data:
  static_volume:
  media_volume:


### Deployment Checklist
bash
# 1. Update settings for production
# 2. Set environment variables
# 3. Install dependencies
pip install -r requirements.txt

# 4. Run migrations
python manage.py migrate

# 5. Collect static files
python manage.py collectstatic --noinput

# 6. Create superuser
python manage.py createsuperuser

# 7. Check deployment
python manage.py check --deploy

# 8. Run tests
python manage.py test

# 9. Start application with gunicorn
gunicorn myproject.wsgi:application


---

## Best Practices

### 1. Project Structure
- Keep apps focused and reusable
- Use meaningful app and model names
- Organize code into models, views, forms, serializers
- Use separate settings files for different environments

### 2. Models
- Always set `__str__()` method
- Use `get_absolute_url()` for URL generation
- Add indexes to frequently queried fields
- Use `select_related()` and `prefetch_related()` to optimize queries
- Override `save()` and `delete()` methods when needed

### 3. Views
- Keep views thin, move logic to models or services
- Use class-based views for standard CRUD operations
- Use function-based views for complex logic
- Always validate user input
- Use `get_object_or_404()` instead of try/except

### 4. Templates
- Use template inheritance (extends)
- Keep templates DRY with includes
- Use custom template tags for complex logic
- Never put business logic in templates

### 5. Forms
- Always use forms for user input
- Use ModelForms when possible
- Validate data in clean methods
- Use CSRF protection

### 6. Security
- Never commit SECRET_KEY to version control
- Use environment variables for sensitive data
- Always set DEBUG=False in production
- Use HTTPS in production
- Enable CSRF protection
- Validate and sanitize user input
- Use Django's built-in authentication

### 7. Performance
- Use database indexes
- Use select_related() and prefetch_related()
- Cache expensive operations
- Use pagination for large datasets
- Optimize queries (use django-debug-toolbar)
- Use database connection pooling

### 8. Testing
- Write tests for models, views, and forms
- Aim for high test coverage
- Use fixtures for test data
- Test edge cases and error handling

### 9. Database
- Always use migrations
- Never edit migration files manually
- Backup database regularly
- Use database transactions for related operations

### 10. Documentation
- Document complex code
- Keep README updated
- Document API endpoints
- Add docstrings to functions and classes

---

*This cheat sheet covers Django 4.x. Always refer to the official Django documentation for the most up-to-date information.*