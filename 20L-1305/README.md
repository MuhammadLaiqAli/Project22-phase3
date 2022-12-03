                                                               #  SECURITY IN DJANGO
 # Cross site scripting (XSS) protection
    
XSS attacks allow a user to inject client side scripts into the browsers of other users. This is usually achieved by storing the malicious scripts in the database 
where 
it will be retrieved and displayed to other users, or by getting users to click a link which will cause the attacker’s JavaScript to be executed by the user’s browser. 
However, XSS attacks can originate from any untrusted source of data, such as cookies or web services, whenever the data is not sufficiently sanitized before including 
in a page.
Using Django templates protects you against the majority of XSS attacks. However, it is important to understand what protections it provides and its limitations.
Django templates escape specific characters which are particularly dangerous to HTML. While this protects users from most malicious input, it is not entirely 
foolproof. 
For example, it will not protect the following:
          <style class={{ var }}>...</style>
If var is set to 'class1 onmouseover=javascript:func()', this can result in unauthorized JavaScript execution, depending on how the browser renders imperfect HTML. 
(Quoting the attribute value would fix this case.) It is also important to be particularly careful when using is_safe with custom template tags, the safe template tag,
mark_safe, and when autoescape is turned off. In addition, if you are using the template system to output something other than HTML, there may be entirely separate 
characters and words which require escaping.You should also be very careful when storing HTML in the database, especially when that HTML is retrieved and displayed.  



# Cross site request forgery (CSRF) protection
    
CSRF attacks allow a malicious user to execute actions using the credentials of another user without that user’s knowledge or consent.
Django has built-in protection against most types of CSRF attacks, providing you have enabled and used it where appropriate. However, as with any mitigation technique,
there are limitations. For example, it is possible to disable the CSRF module globally or for particular views. You should only do this if you know what you are doing. 
There are other limitations if your site has subdomains that are outside of your control.CSRF protection works by checking for a secret in each POST request. 
This ensures that a malicious user cannot “replay” a form POST to your website and have another logged in user unwittingly submit that form. 
The malicious user would have to know the secret, which is user specific (using a cookie).When deployed with HTTPS, CsrfViewMiddleware will check that the HTTP 
referer header is set to a URL on the same origin (including subdomain and port). Because HTTPS provides additional security, it is imperative to ensure connections 
use 
HTTPS where it is available by forwarding insecure connection requests and using HSTS for supported browsers.
Be very careful with marking views with the csrf_exempt decorator unless it is absolutely necessary.


# SQL injection protection
    
SQL injection is a type of attack where a malicious user is able to execute arbitrary SQL code on a database. This can result in records being deleted or data leakage.

Django’s querysets are protected from SQL injection since their queries are constructed using query parameterization. A query’s SQL code is defined separately from the 
query’s parameters. Since parameters may be user-provided and therefore unsafe, they are escaped by the underlying database driver.Django also gives developers power 
to write raw queries or execute custom sql. These capabilities should be used sparingly and you should always be careful to properly escape any parameters that the 
user
can control. In addition, you should exercise caution when using extra() and RawSQL.


# User-uploaded content
    
If your site accepts file uploads, it is strongly advised that you limit these uploads in your web server configuration to a reasonable size in order to prevent denial 
of service (DOS) attacks. 
In Apache, this can be easily set using the LimitRequestBody directive.If you are serving your own static files, be sure that handlers like Apache’s mod_php, 
which would execute static files as code, are disabled. You don’t want users to be able to execute arbitrary code by uploading and requesting a specially crafted file.
Django’s media upload handling poses some vulnerabilities when that media is served in ways that do not follow security best practices. Specifically, an HTML file can 
be uploaded as an image if that file contains a valid PNG header followed by malicious HTML. This file will pass verification of the library that Django uses for
ImageField image processing (Pillow).

When this file is subsequently displayed to a user, it may be displayed as HTML depending on the type and configuration of your web server.
No bulletproof technical solution exists at the framework level to safely validate all user uploaded file content, however, there are some other steps you can take 
to mitigate these attacks:

One class of attacks can be prevented by always serving user uploaded content from a distinct top-level or second-level domain. This prevents any exploit blocked by 
same-origin policy protections such as cross site scripting. For example, if your site runs on example.com, you would want to serve uploaded content (the MEDIA_URL 
setting) from something like usercontent-example.com. It’s not sufficient to serve content from a subdomain like usercontent.example.com.
Beyond this, applications may choose to define a list of allowable file extensions for user uploaded files and configure the web server to only serve such files.

# Clickjacking protection
    
Clickjacking is a type of attack where a malicious site wraps another site in a frame. 
This attack can result in an unsuspecting user being tricked into performing unintended actions on the target site.Django contains clickjacking protection in
the form of the X-Frame-Options middleware which in a supporting browser can prevent a site from being rendered inside a frame. It is possible to disable the
protection on a per view basis or to configure the exact header value sent.The middleware is strongly recommended for any site that does not need to have its 
pages wrapped in a frame by third party sites, or only needs to allow that for a small section of the site.
                  
                                                     # SECRETS MANAGEMENT IN DJANGO
                                                      
  # 1.STEP 1
  
## Replace plaintext secrets with references    

Collect all secrets from your settings.py, orchestrator manifests, .env file, or any other config file, and use the SecretHub CLI to encrypt and centrally store them.
In your Python code there’s no need to import a library or SDK of some sort. You can just read secrets from environment variables.

    settings.py
    import os
    DB_USER = os.getenv('DB_USER')

    DB_PASSWORD = os.getenv('DB_PASSWORD')

    API_KEY = os.getenv('API_KEY')

To specify the secrets your Django app needs, create a file named secrethub.env:

    secrethub.env

    DB_USER     = {{ company/app/db/user }}
    DB_PASSWORD = {{ company/app/db/password }}
    API_KEY     = {{ company/app/api/key }}
    
If you’re familiar with .env files in Python: you can see a secrethub.env file as a templated version of a regular .env file that can now be checked into Git 
and shared freely with the world, as it holds no secrets!                                             

    STEP 2
    Load secrets into your Django app the moment it starts
    
To automatically load the referenced secrets on startup, all you need to do is include the SecretHub CLI in your Django application launch script.

               Before                                                    After
            manage.py runserver                               secrethub run -- manage.py runserver
          
          
# STEP 3
## Control & monitor secret reads done by your Django app

Every time your Django app starts, secret reads gets recorded on the audit log. Restrict access to only the secrets it needs and know that you can revoke 
access with a single command.
                                                                                                                                            
                                                                                                                                            
                                                # DOCUMENTATION OF DJANGO
                                                                                                                                            
       
## User manuals

   
 Django  has also a large set of document for their user which inintially starts from installation part and then go forward to all the functions and method like
 database connectivity, sql injection, csrf tokens ,user authentication and many are explained by django in their documentation.
    
Django User Guide is a django>=1.6(version) app that shows configurable, self-contained HTML guides to users. Showing a guide to all of your users is as easy as
creating a `Guide` object and linking them to your users. Use the convenient `{% user_guide %}` template tag where you want guides to appear and Django User Guide does 
the rest. 

When a user visits a page containing the template tag, they are greeted with relevant guides. Django User Guide decides what guide(s) a user needs to see and displays 
them in a modal window with controls for cycling through those guides. Django User Guide tracks plenty of meta-data: creation times, guide importance, if the guide has 
been finished by specific users, finished times, etc.

Django User Guide decides what guide(s) a user needs to see and displays them in a modal window with controls for cycling through those guides. Django User Guide 
tracks plenty of meta-data: creation times, guide importance, if the guide has been finished by specific users, finished times, etc.

## Django Tutorial
        
 Django has also their tutorials that are not officially from django but these are third party tutorials like w3schools and many tech channels on youtube are 
 covering the content of django which ultimately supports the community using django.
 
## Django  Installation guide
Django has complete set of user guide that is enough for any beginner to start his journey with python django. This guide completely elaborate the installation process
with all the dependencies that are required to run the set up and guidance in all configuration settings.

Following is the sample of installation process of Django:
  
## Install Python

Django is a Python web framework. See What Python version can I use with Django? for details.
Get the latest version of Python at https://www.python.org/downloads/ or with your operating system’s package manager.

## Install Apache and mod_wsgi

If you just want to experiment with Django, skip ahead to the next section; Django includes a lightweight web server you can use for testing, so you won’t need to set 
up Apache until you’re ready to deploy Django in production.

If you want to use Django on a production site, use Apache with mod_wsgi. mod_wsgi operates in one of two modes: embedded mode or daemon mode. In embedded mode, 
mod_wsgi is similar to mod_perl – it embeds Python within Apache and loads Python code into memory when the server starts. Code stays in memory throughout the life of 
an Apache process, which leads to significant performance gains over other server arrangements. In daemon mode, mod_wsgi spawns an independent daemon process that 
handles requests. The daemon process can run as a different user than the web server, possibly leading to improved security. The daemon process can be restarted 
without restarting the entire Apache web server, possibly making refreshing your codebase more seamless. Consult the mod_wsgi documentation to determine which mode is 
right for your setup. Make sure you have Apache installed with the mod_wsgi module activated. Django will work with any version of Apache that supports mod_wsgi.


See How to use Django with mod_wsgi for information on how to configure mod_wsgi once you have it installed.

If you can’t use mod_wsgi for some reason, fear not: Django supports many other deployment options. One is uWSGI; it works very well with nginx. Additionally, Django 
follows the WSGI spec (PEP 3333), which allows it to run on a variety of server platforms.      


## Get your database running
      
If you plan to use Django’s database API functionality, you’ll need to make sure a database server is running. Django supports many different database servers and is 
officially supported with PostgreSQL, MariaDB, MySQL, Oracle and SQLite.

If you are developing a small project or something you don’t plan to deploy in a production environment, SQLite is generally the best option as it doesn’t require 
running a separate server. However, SQLite has many differences from other databases, so if you are working on something substantial, it’s recommended to develop with 
the same database that you plan on using in production.

In addition to the officially supported databases, there are backends provided by 3rd parties that allow you to use other databases with Django.

In addition to a database backend, you’ll need to make sure your Python database bindings are installed.

If you’re using PostgreSQL, you’ll need the psycopg2 package. Refer to the PostgreSQL notes for further details.
If you’re using MySQL or MariaDB, you’ll need a DB API driver like mysqlclient. See notes for the MySQL backend for details.
If you’re using SQLite you might want to read the SQLite backend notes.
If you’re using Oracle, you’ll need a copy of cx_Oracle, but please read the notes for the Oracle backend for details regarding supported versions of both Oracle and 
cx_Oracle.

If you’re using an unofficial 3rd party backend, please consult the documentation provided for any additional requirements.
If you plan to use Django’s manage.py migrate command to automatically create database tables for your models (after first installing Django and creating a project), 
you’ll need to ensure that Django has permission to create and alter tables in the database you’re using; if you plan to manually create the tables, you can grant 
Django SELECT, INSERT, UPDATE and DELETE permissions. After creating a database user with these permissions, you’ll specify the details in your project’s settings 
file, see DATABASES for details.

If you’re using Django’s testing framework to test database queries, Django will need permission to create a test database.

##Install the Django code

Installation instructions are slightly different depending on whether you’re installing a distribution-specific package, downloading the latest official release, or 
fetching the latest development version.

Installing an official release with pip
This is the recommended way to install Django.

Install pip. The easiest is to use the standalone pip installer. If your distribution already has pip installed, you might need to update it if it’s outdated. If it’s 
outdated, you’ll know because installation won’t work.

Take a look at venv. This tool provides isolated Python environments, which are more practical than installing packages systemwide. It also allows installing packages 
without administrator privileges. The contributing tutorial walks through how to create a virtual environment.

After you’ve created and activated a virtual environment, enter the command:


$ python -m pip install Django

                                                        #Django-extensions documentation!

Django Extensions is a collection of custom extensions for the Django Framework.
These include management commands, additional database fields, admin extensions and much more.

You can get Django Extensions by using pip:

$ pip install django-extensions
If you want to install it from source, grab the git repository and run setup.py:

$ git clone git://github.com/django-extensions/django-extensions.git
$ cd django-extensions
$ python setup.py install
Then you will need to add the django_extensions application to the INSTALLED_APPS setting of your Django project settings.py file.

For more detailed instructions check out our Installation instructions. Enjoy.

This might mean the django-extensions may work with older or unsupported versions but it is not guarantee and most likely will not fix bugs related to 
incompatibilities with older versions.
