# Python + Django + Tailwind

create a folder on desktop or wherever you want it save and right click and open in terminal


py --version
    check if python exist (if you see a version number then it means to say it has)

installing node (a requesite for installing tailwind)
    node -v (this will check the version)
    winget install OpenJS.NodeJS.LTS (if you don't have node installed on your machine)

### START HERE IF EVERYTHING ABOVE IS OKAY ###
Create a python virtual environment
    py -m venv venv (on windows)
    python -m venv venv (on mac) 
        if this would not work, run: 
        which python3 && python3 --version
        python3 -m venv venv


Activating venv
    venv/Scripts/activate (on windows)
    source venv/bin/activate (on mac)

    if error says UnauthorizeAccess run the command: Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass

Install django
    pip install Django (works both on mac and windows)

### START HERE IF YOU WILL BE USING THE SAME FOLDER WITH ENV INSTALLED IN IT
Make a project
    django-admin startproject mywebsite
    cd mywebsite

After creating and navigating into your project directory, you can run your specified command to install the django-tailwind package and its extras into your project's environment.


    pip install 'django-tailwind[cookiecutter,honcho,reload]'


This command installs django-tailwind with development dependencies like django-browser-reload for live reloads.

Add apps to settings.py:
In your settings.py file, add 'tailwind' and the conditional 'django_browser_reload' to INSTALLED_APPS.
python

INSTALLED_APPS = [
    # ... default apps
    "django.contrib.staticfiles",
    # Third-party apps
    "tailwind",
    # ... other apps
]

if DEBUG:
    INSTALLED_APPS += ["django_browser_reload"]


Add Middleware and URLs:
Add the django-browser-reload middleware to settings.py and include its URLs in your project's urls.py.
python

# settings.py
if DEBUG:
    MIDDLEWARE += [
        "django_browser_reload.middleware.BrowserReloadMiddleware",
    ]

# urls.py (PROJECT FILE)
from django.urls import include, path
from django.conf import settings

urlpatterns = [
    path("admin/", admin.site.urls),
    # ... other paths
]

if settings.DEBUG:
    urlpatterns += [
        path("__reload__/", include("django_browser_reload.urls")),
    ]
 
make sure that you are in your cd projectfolder

### Initialize Tailwind
Create a Tailwind app (theme):
Run the initialization command, which automatically creates a Django app (e.g., named "theme") and sets up the required Tailwind files.
Run this command:       

    python manage.py tailwind init

    if error shows like: 
    no module named theme, run the command: pip install django-theme

### Configure the theme app:
After initialization, add the generated app (e.g., "theme") to INSTALLED_APPS and specify it in settings.py:
python

# settings.py
INSTALLED_APPS = [
    # ...
    "tailwind",
    "theme", # Your new theme app
]
TAILWIND_APP_NAME = "theme"


### Install Tailwind CSS dependencies:
Navigate to your project root

Run this command:

    python manage.py tailwind install

This installs the latest Tailwind CSS via npm within your theme app's static source directory. 


### Check Tailwind CSS to Your Templates 
Update your base template:
In your main HTML template (e.g., templates/base.html), load the tailwind_tags and include the compiled CSS in the 

<head> section.
html
{% load static tailwind_tags %}
<!DOCTYPE html>
<html lang="en">
<head>
    <!-- ... other head elements ... -->
    <title>My Django App with Tailwind</title>
    {% tailwind_css %}
</head>
<body>
    <!-- ... your content ... -->
</body>
</html>
 
### Run the Development Server
Start Django and Tailwind simultaneously:
The recommended method to run both the Django server and the Tailwind watcher (for live updates) is:

Run this command:
    python manage.py tailwind dev


##### DONE INSTALLING AND SETTING UP #####
python manage.py tailwind dev 
BASE THEME SHOULD NOT DISPLAY ANYTHING

Done! Your Django app should now serve the base theme at 127.0.0.1:8000/. The issue was:

No root URL handler — Your urls.py only had the admin path

### ADD THIS ON urls.py (urls.py which is found in base/project name )

    path('', include('theme.urls')),  # Include this in the project urls.py 

No theme app URLs — The theme app had no views or URL configuration

### CREATE views.py and urls.py in theme folder ####

    CREATE a views.py — a simple index view that renders your base.html template

        create the views.py and ADD THIS (theme)
        from django.shortcuts import render

        def index(request):
            return render(request, 'base.html')


    CREATE the urls.py and ADD THIS  (theme)

        from django.urls import path
        from . import views

        urlpatterns = [
            path('', views.index, name='index'),
        ]

   
Refresh your browser and it should display your Tailwind-styled page with the "Django + Tailwind = ❤️" heading.

## TESTING ON ADDING TEMPLATE ## 

AFTER ADDING LIKE: mypage.html (which is access on: http://127.0.0.1:8000/mypage/)

    create the html "mypage.html" in theme
        <h1>Test</h1> ## add this tag and test the page  http://127.0.0.1:8000/mypage/

    URL PATTERN BELOW MUST BE ADDED ON urls.py inside theme folder

    path('mypage/', views.mypage, name='mypage'),

    and add this code in views.py (inside theme folder)

    def mypage(request):
        return render(request, 'mypage.html')


### AFTER MAKING YOUR TEMPLATE IN templates folder ###

AFTER MAKING YOUR TEMPLATE IN templates folder 

we need to run the npm commands from the static_src directory where the package.json is located, not from the project root.

run these commands:

    cd theme/static_src
    npm run build

Or for the live watcher:

    cd theme/static_src
    npm run dev

The package.json is in static_src, so npm needs to be running from there. 

NOTE: python manage.py tailwind dev (must still be running, open another terminal for your other commands)

### CREATE PYTHON APPLICATION ###

run the command: python manage.py startapp APPNAME

    python manage.py startapp myapp

### run the migration command ###

    python manage.py migrate
        this will generate the default database scheme (db.sqlite3) like user info, group info and etc
        you may then check this url: http://127.0.0.1:8000/admin/login/?next=/admin/

    
### CREATE A SUPER USER ###

    RUN THE COMMAND: 
    python manage.py createsuperuser

### IF PORT IS STILL RUNNING ISSUE ###
KILLING RUNNING PORT ON MAC

    ps aux | grep -i manage 
    THEN LOOK FOR PID NUMBER
    kill -9 PID_NUMBER
    
KILLING RUNNING PORT ON WINDOWS
    tasklist | /i manage
    kill -9 PID_NUMBER
    