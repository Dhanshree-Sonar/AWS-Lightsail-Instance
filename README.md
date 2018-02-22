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
    
## Configuration

1. Create user `grader`
  - Go To AWS lightsail instance
  - Click 'Connect using SSH' button. You will login as user 'ubuntu'
  - `$ sudo adduser grader` (Provide password for user)
  - Check user created with finger command `$ finger grader`
  - Give sudo access to `grader`:
      - `$ sudo cp /etc/suders.d/90-cloud-init-users /etc/suders.d/grader`
      - `$ sudo cat /etc/suders.d/grader`
      - Replace word `ubuntu` to `grader`
      
  2. Create an SSH key pair for grader using the `ssh-keygen` tool
    - Generate SSH key pair on your local system using command `$ ssh-keygen`
    - Give file name '/Users/<user-name>/.ssh/lightsail' and enter passpharase
    - Connect to lightsail instance using 'Connect using SSH' button
    - `$ su grader` to login as grader and enter password
    - `$ mkdir .ssh` to create direcory `.ssh`
    - `$ touch .ssh/authorized_keys`
    - Open terminal on local system and copy content of `/Users/<user-name>/.ssh/lightsail.pub`
    - `$ nano .ssh/authorized_keys` run command on your lightsail instance and paste content
    - `$ chmod 700 .ssh` to make sure no one other than root can access you `.ssh` folder
    - `$ chmod 644 .ssh/authorized_keys` to restrict access to your ssh-key
    - Open terminal on local system and connect to `grader` using command `$ ssh grader@<ip-address> -i ~/.ssh/lightsail`
   
  3. Update all currently installed packages
    - `$ sudo apt-get update`
    - `$ sudo apt-get upgrade`
  
  4. Change the SSH port from 22 to 2200, disable ssh login for root, and enforce key based authentication
    - Go to AWS Lightsail instance -> Networking -> added port -> Custom TCP 2200
    - `$ sudo nano /etc/ssh/sshd_config` 
    - Find the `Port` line and replace `22` with `2200`
    - Find the `PermitRootLogin` line and replace `prohibit-password` with `no`
    - Find the `PasswordAuthentication` line and replace `yes` with `no`
    - `$ sudo service ssh restart`
    
  5. Configure the Uncomplicated Firewall (UFW)
    - `$ sudo ufw default deny incoming`
    - `$ sudo ufw default allow outgoing`
    - `$ sudo ufw allow 2200/tcp`
    - `$ sudo ufw allow 80/tcp`
    - `$ sudo ufw allow 123/udp`
    - `$ sudo ufw enable`
    
  6. Configure the local timezone to UTC
    - `$ sudo timedatectl set-timezone Etc/UTC`
    
  7. Create a new database user and database
    - `$ sudo su – postgres` to login as postgres user. Postgres user is an admin for postgresql.
    - `$ psql` to enter into postgresql database
    - `postgres=# CREATE USER catalog WITH PASSWORD 'catalog';`
    - `\q` to exit postgres terminal
    - `$ createdb catalog -O catalog` to create database `catalog` which is owned by user 'catalog'
    
  8. Clone FurnitureCatalog github repository
    - `$ sudo mkdir /var/www/FlaskApp`
    - `$ cd /var/www/FlaskApp`
    - `$ sudo git clone https://github.com/Dhanshree-Sonar/Furniture-Catalog.git`
   
  9. Configure and enable new virtual host
    - `$ sudo nano /etc/apache2/sites-available/FlaskApp.conf`
    - Add content:</br>
      ```
      <VirtualHost *:80>
        ServerName 18.220.175.105
        ServerAdmin sonardhanshree@gmail.com
        WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
        <Directory /var/www/FlaskApp/Furniture-Catalog/>
          Order allow,deny
          Allow from all
        </Directory>
        Alias /static /var/www/FlaskApp/Furniture-Catalog/static
        <Directory /var/www/FlaskApp/FurnitureCatalog/static/>
          Order allow,deny
          Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>
      ```
     - `$ sudo service apache2 reload`
     - `$ sudo a2ensite FlaskApp`
     
   10. Create .wsgi file
    - `$ cd /var/www/FlaskApp`
    - `sudo nano flaskapp.wsgi`
    - Add content
      ```
      #!/usr/bin/python
			import sys
			import logging
			logging.basicConfig(stream=sys.stderr)
			sys.path.insert(0, "/var/www/FlaskApp/")

      from Furniture-Catalog import app as application
      application.secret_key = 'super_secret_key'
      ```
    - `$ sudo service apache2 restart`
   
   11. Change old `create_engine` statement to access secured catalog database
    - Replace old `create_engine` statement with `engine = create_engine('postgresql://catalog:catalog@localhost/catalog')`
    - Make chnages to `__init__.py` and `database_setup.py`
   
   12. Make `client_secrets.json` location absolute
    - Replace `client_secrets.json` with `/var/www/FlaskApp/FurnitureCatalog/client_secrets.json`
    
   13. Disabled default apache settings
    - `$ sudo a2dissite 000-default`
    - `$ sudo service apache2 restart`
    
   14. Go to Google Developer Console and add paths to 'Authorised JavaScript origins' and 'Authorised redirect URIs'
    - Add below lines to Authorised JavaScript origins
      ```
      http://ec2-18-220-175-105.us-east-2.compute.amazonaws.com
      http://18.220.175.105
      ```
    - Add below lines to Authorised redirect URIs
      ```
      http://ec2-18-220-175-105.us-east-2.compute.amazonaws.com/
      http://ec2-18-220-175-105.us-east-2.compute.amazonaws.com/login
      http://ec2-18-220-175-105.us-east-2.compute.amazonaws.com/gconnect
      ```
    
    15. Download new `client_secrets.json` and replace with old file
      - Download `client_secrets.json` and copy the file content
      - `$ sudo nano /var/www/FlaskApp/FurnitureCatalog/client_secrets.json`
      - Remove old content and paste new `client_secrets.json` content
      
## References
	- https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu
	- https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
