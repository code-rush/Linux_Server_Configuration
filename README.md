Linux Server Configuration
==========================

Walkthrough to configure Linux Server to host a web application
---------------------------------------------------------------

### 1) Launch Virtual Machine(with your Udacity Account) and SSH into the server

1. Create new development Environment.
2. Download your private key from the given link and write down your public IP address.
If you are unable to download the private key, open the file in the browser and copy the contents of the file to a new document and save the document as `.rsa` file. You can give whatever name you wish to give to that file.
3. Move the file containing the private key to `~/.ssh` folder.
	* `$ mv *location-of-the-.rsa-file* ~/.ssh/`
4. SSH into the server.
	* `$ ssh root@YOUR-PUBLIC-IP-ADDRESS -i ~/.ssh/PRIVATE-KEY-FILE`


### 2) Creating a new user and providing the user permissions to sudo

1. Create new user.
	`$ adduser grader`
Here the name of the user is grader.
2. Giving new user the permission to sudo and ask for their password atleast once, execute the following commands.
	* `$ visudo`
	* write the following line in the file below `root ALL=(ALL:ALL) ALL` and save it.
		* `grader ALL=(ALL:ALL) ALL`


_Logout from the root and ssh with "grader"_ .


### 3) Updating Available Packages

_After logging with the "grader"_.

1. Get the update list.
	* `$ sudo apt-get update`
2. Upgrading the installed packages:
	* `$ sudo apt-get upgrade`

__Getting rid of warning messages `sudo: unable to resolve host... `__.

_You have to be logged in as root_.

1. Execute the following command.
	* `$ hostname`
2. Copy the output of the above command.
3. Open the file of hosts.
	* `$ nano /etc/hosts`
4. Paste the details as shown below.
	* `127.0.0.1 localhost`
	* `127.0.1.1 PASTE-HERE`
	   and save the file.


### 4) Generating public and private keys to allow "grader" access to the server and disabling access using password

1. Open new Terminal. Make sure you are on the local machine.
2. Generating SSH key pair.
	* `$ ssh-keygen`
3. You will be asked to provide file name. Give the name of the file you like, but make sure to put that file under directory.
	* `/Users/THE-USER-UNDER-WHICH-YOU-ARE-LOGGED-IN/.ssh/FILE-NAME`
4. You will be asked to give a pass-phrase.
5. Key pairs are now generated. The one that ends with .pub extension is a public key and other is private key.
6. Switch back to the terminal where you are logged in as grader on the server.
7. Make a new file 'authorized_keys' in a new directory '.ssh'.
	* `$ mkdir /home/grader/.ssh`
	* `$ touch /home/grader/authorized_keys`
8. Copy the contents of the public key to the authorized_keys file and save the file.
9. Before disabling user access without password, make sure the keys work like expected.
	* Logout from the server.
	* ssh to the server using the key.
		* `$ ssh grader@YOUR-PUBLIC-IP-ADDRESS -i ~/.ssh/YOUR-PRIVATE-KEY-FILE-NAME`
	* You will be asked to enter the pass-phrase that you used earlier while creating the keys. Enter the pass-phrase.
	* If you logged in to the server, key pair works and your identity has been added to the server.
10. Disabing password authentication for grader.
	* `$ sudo nano /etc/ssh/sshd_config`
	* Change `PasswordAuthentication` from `yes` to `no`
11. Disabling root access.
	* Change `PermitRootLogin` to `no` from `without-password`



### 5) Changing SSH port from 22 to 2200

1. nano into sshd_config file.
	* `$ nano /etc/ssh/sshd_config`
2. Change Port 22 to Port 2200 and save the file.
3. Restart the ssh service.
	* `$ sudo service ssh restart`


### 6) Configuring Uncomplicated Firewall (ufw) to allow only specified incoming connections

1. Deny all incoming connections (Make sure firewall is inactice).
	* To check ufw status:
		* `$ sudo ufw status`
	* Denying all incoming connections:
		* `$ sudo ufw default deny incoming`
2. Allow incoming connections for SSH(port 2200), HTTP(port 80) and NTP(port 123).
	* `$ sudo ufw allow 2200`
	* `$ sudo ufw allow 80`
	* `$ sudo ufw allow 123`
3. Now, activate firewall. (Make sure to allow connection for ssh before activating the firewall).
	* `$ sudo ufw enable`



### 7) Configuring local timezone to UTC

1. Open the timezone selection dialog.
	* `$ sudo dpkg-reconfigure tzdata`
2. Choose none of the above and then UTC.


### 8) Installing and Configuring Aapache to server a Python and mod_wsgi application

1. Install Apache server.
	* `$ sudo apt-get install apache2`
2. Go to your PUBLIC-IP-ADDRESS in a browser and it should say __"It Works!"__.
3. Installing dependencies. 
	* `$ sudo apt-get install python-setuptools libapache2-mod-wsgi`
4. Restart Apache server for __mod_wsgi__ to load.
	* `$ sudo service apache2 restart`
5. Getting rid of the error message "Could not reliably determine the server's...".
	*  `$ sudo nano /etc/apache2/conf-available/YOUR-HOSTNAME.conf`
	*  Write -> ServerName YOUR-HOSTNAME in the file and save it.
	*  `$ sudo a2enconf YOUR-HOSTNAME`
	*  `$ sudo service apache2 reload`



### 9) Installing and Configuring git

1. Install Git.
	* `$ sudo apt-get install git`
2. Configure git with your name and email address.
	* `$ git config --global user.name "YOUR USERNAME"`
	* `$ git config --global user.email "YOUR EMAIL ADDRESS"`



### 10) Configuration setup to deploy Flask Application

1. Installing and enabling mod_wsgi.
	* `$ sudo apt-get install libapache2-mod-wsgi python-dev`
2. Enable mod_wsgi.
	* `$ sudo a2enmod wsgi`.
3. Creating Flask App
	* First cd into /var/www directory.
		* `$ cd /var/www`
	* Create a new directory and give a name to the directory.
		* `$ sudo mkdir FLASK-APP-DIR-NAME`
	* Create a new directory inside the FLASK-APP-DIR-NAME and give the same name.
		* `$ cd FLASK-APP-DIR-NAME`
		* `$ sudo mkdir FLASK-APP-DIR-NAME`
	* Move inside this directory and create two subdirectories named _static_ and _templates_ .
		* `$ cd FLASK-APP-DIR-NAME`
		* `$ sudo mkdir static templates`
	* Create file that will contain flask application logic and the logic to it.
		* `$ sudo nano __init__.py` and copy the following code and save it.
		
```python
from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
	return "Hello, World!"
if __name__ == "__main__":
    	app.run()
```

4. Installing flask
	* Install pip which we will use to install virtual environment and flask.
		* `$ sudo apt-get install python-pip`
	* Install _virtualenv_.
		* `$ sudo pip install virtualenv`
	* Execute the following command where _venv_ is the name you would like to give to your temp environment.
		* `$ sudo virtualenv venv`
	* Install Flask in that environment by activating the virtual environment by executing following command.
		* `$ source venv/bin/activate` (where _venv_ is the name you gave to the virtual environment)
	* Execute the following commands to install Flask inside.
		* `$ sudo pip install Flask`
	* Execute the following command and test if the installation is successfull and the app is running.
		* `$ sudo python __init__.py`
		* Open the browser and go to YOUR-PUBLIC-IP-ADDRESS and the app should be running if everything is working fine.
	* To deactivate the environment, execute the following command.
		* `$ deactivate`
5. Configuring and enabling a new virtual host
	* Issue the following command.
		* `$ sudo nano /etc/apache2/sites-available/FLASK-APP-DIR-NAME.conf`
	* Add the following lines of code to the file to configure the virtual host. Be sure to make the changes and save it.

		```
		<VirtualHost *:80>
		ServerName YOUR-PUBLIC-IP-ADDRESS
		ServerAdmin USER@PUBLIC-IP-ADDRESS
		WSGIScriptAlias / /var/www/FLASK-APP-DIR-NAME/FLASK-APP-DIR-NAME.wsgi
		<Directory /var/www/FLASK-APP-DIR-NAME/FLASK-APP-DIR-NAME/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/FLASK-APP-DIR-NAME/FLASK-APP-DIR-NAME/static
		<Directory /var/www/FLASK-APP-DIR-NAME/FLASK-APP-DIR-NAME/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
		</VirtualHost>
		```
6. Enable the virtual host with the following command.
	`$ sudo a2ensite FLASK-APP-DIR-NAME`
7. Creating the .wsgi File:
	* Move to the /var/www/FLASK-APP-DIR-NAME directory and create a file names FLASK-APP-DIR-NAME.wsgi.
		* `$ cd /var/www/FLASK-APP-DIR-NAME`
		* `$ sudo nano FLASK-APP-DIR-NAME.wsgi`
	* Add the following lines of code to the FLASK-APP-DIR-NAME.wsgi file.
		```python
		#!/usr/bin/python
		import sys
		import logging
		logging.basicConfig(stream=sys.stderr)
		sys.path.insert(0,"/var/www/FLASK-APP-DIR-NAME/")
			
		from FLASK-APP-DIR-NAME import app as Application
		application.secret_key = 'Add your secret key'
		```
8. Restart Apache.
	* `$ sudo service apache2 restart`



### 11) Installing PostgreSQL

1. Execute the following command to install PostgreSQL:
	* `$ sudo apt-get install postgresql postgresql-contrib`



### 12) Cloning and Configuring a Flask project from GitHub repository

1. Go the FLASK-APP-DIR which you created while setting up FLASK APP:
	* `$ cd /var/www/FLASK-APP-DIR-NAME/FLASK-APP-DIR-NAME`
2. Clone a flask app project repo:
	* `$ git clone http://github.com/YOUR-USERNAME/FLASK-APP-REPO.git`
3. Move main flask app file contents to `__init__.py` by:
	* `$ mv project.py __init__.py`
4. Create the hiearchy as needed for Flask Application to work fine.
5. Making the GitHub repository inaccessbile by public:
	* Create `.htaccess` file and write `RedirectMatch 404 /\.git` and save it:
		* `$ sudo nano /var/www/FLASK-APP-DIR-NAME/.htaccess`




### 13) Installing packages needed for the application to run:

1. Activate vitrual environment by executing following code:
	* `$ source venv/bin/activate` -> where _venv_ is the name you gave while creating the Environment.
2. Install all the packages from `requirements.txt` file:
	* `$ pip install -r requirements.txt`
3. Install the following packages globally:
	* `$ sudo pip intsall flask-seasurf`
	* `$ sudo pip install sqlalchemy`
	* `$ sudo apt-get install python-psycopg2`
	* `$ sudo pip install flasl`
	* `$ sudo pip install oauth oauth2client`


### 14) Configuring PostgreSQL

1. Check that no remote connections are allowed(default):
	* `$ sudo nano /etc/postgresql/9.3/main/pg_hba.conf`
2. Change lines in the .py files where the line executes the connection to the databse:
	* `engine = create_engine('postgresql://item_catalog:PASSWORD-FOR-DATABASE@localhost/item_catalog)`
3. Create needed linux user for psql:
	* `sudo adduser item_catalog`
4. Change to default user postgres:
	* `sudo su - postgres`
5. Connect to the system:
	* `$ psql`
6. Add postgres user with password:
	* Create user with login role and set a password:
		* ` create USER catalog with password 'PASSWORD-FOR-DATABASE';`
	* Allow the user to create database tables:
		* ` alter USER catalog CREATEDB;`
7. Create database:
	* `create database item_catalog with OWNER item_catalog;`
8. Connect to the database item_catalog:
 	* ` \c item_catalog`
9. Revoke all rights:
	* `revoke all on schema public from public;`
10. Grant access to only item_catalog role:
	* `grant all on schema public to item_catalog;`
11. Exit:
	* `\q`
12. Exit from postgres user:
	* `$ exit`
13. Create the database:
	* `$ python database_setup.py`



### 15) Run Application

1. Restart Apache server:
	* `$ sudo service apache2 restart`
2. Go to YOUR-PUBLIC-IP-ADDRESS from a browser and enjoy. 


** If Something is wrong and the application is not working then check error logs and find solutions for the errors. **
* Checking error logs by following command: `$ sudo tail -20 /var/log/apache2/error.log` *








