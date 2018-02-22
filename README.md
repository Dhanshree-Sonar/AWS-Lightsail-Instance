# AWS-Lightsail-Instance
Created a brand-new, barebone, AWS Linux instance and turned it into the secure and efficient web server, which is hosting a Flask application. 

## IP Address and SSH Port
- AWS Lightsail Server IP Address : 18.220.175.105
- SSH Port : 2200

## Web Application URL
http://ec2-18-220-175-105.us-east-2.compute.amazonaws.com/

## Packages Installed
  - **Apache**</br>
    Apache is a free open source software which runs over 50% of the world’s web servers.</br>
    `$ sudo apt-get install apache2`</br>
    Make .git folder inaccessible via web browser:
      - `$ sudo nano /etc/apache2/apache2.conf`
      - Add line: `RedirectMatch 404 /.git`
      - `$ sudo service apache2 restart`
  - **Mod_wsgi**</br>
    WSGI (Web Server Gateway Interface) is an interface between web servers and web apps for python. Mod_wsgi is an Apache HTTP server mod that enables Apache to serve Flask applications.</br>
    `$ sudo apt-get install libapache2-mod-wsgi python-dev`</br>
    Enable mod_wsgi:</br>
      - `$ sudo a2enmod wsgi`</br>
  - **PostgreSQL**</br>
    Postgresql is a relational database management system that provides an implementation of the SQL querying language.</br>
    `$ sudo apt-get install postgresql postgresql-contrib`</br>
    Disable remote connections:</br>
      - `$ sudo cat /etc/postgresql/9.5/main/postgresql.conf`</br>
      - Replace line `listen_addresses = '*'` with `listen_addresses = 'localhost'` (Bydefault the remote connection disabled.)
  - **Git**</br>
    Git is a version control system which allows you to keep track of your software at the source level.</br>
    `$ sudo apt-get install git`</br>
  - **Pip**</br>
    Pip is a recommended tool for installing Python packages.</br>
    `$ sudo apt-get install python-pip`</br>
  - **Flask**</br>
    Flask is a micro webdevelopment framework for Python.</br>
    `$ sudo pip install flask`</br>
  - **SQLAlchemy**</br>
    The SQLAlchemy SQL Toolkit and Object Relational Mapper is a comprehensive set of tools for working with databases and Python.</br>
    `$ sudo pip install SQLAlchemy`</br>
  - **Psycopg2**</br>
    Psycopg2 is a PostgreSQL database driver that serves as a Python client for access to the PostgreSQL server.</br>
    `$ sudo pip install psycopg2`</br>
    `sudo -H pip install psycopg2-binary`</br>
  - **Oauth2client**</br>
    Oauth2client makes it easy to interact with OAuth2-protected resources, especially those related to Google APIs.</br>
    `$ sudo -H pip install --upgrade oauth2client`</br>
  - **Requests**</br>
    Requests is the only Non-GMO HTTP library for Python, safe for human consumption.</br>
    `$ sudo -H  pip install requests`</br>
  - **Finger**</br>
    Linux finger command prints user information in the system.</br>
    `$ sudo apt-get install finger`</br>
