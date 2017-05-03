# Linux_server

* Amazon Lightsail terminal:
  * Signup for Amazon Lightsail.
  * Launch Ubuntu terminal.
  
* create a new user:
  * sudo adduser grader
  * And add the details mentioned
  
* Proiding grader permission
  * sudo visudo
  * Inside that add grader ALL=(ALL:ALL) ALL 
  * save file
  * Add grader to/etc/suoders.d/
  * In it write grader ALL=(ALL:ALL) ALL
  * Add root to /etc/sudoers.d/ and again write the above command.

* Update and Upgrade
  * sudo apt-get update
  * sudo apt-get upgrade

* Changing SSH config:
  * change port number to 2200
  * In file change PermitRootLogin prohibit-password to PermitRootLogin no to disallow root login
  * change PasswordAuthentication to yes.
  * save file(nano: ctrl + x, y, Enter) and restart.
  
* Create SSH keys and copy to server manually:
   * On the local machine generate SSH key pair: ssh-keygen
   * save the keygen file in your ssh directory /home/ubuntu/.ssh/
   * For privacy reason you can add password
   * Change the SSH port number configuration in Amazon lightsail in networking tab to 2200
   * login, make .ssh directory mkdir.ssh and then make ame a file to store key /authorised_keys
   * On your local machine read contents of the public key cat .ssh/project5.pub
   * copy the file you created in grader nano .ssh/authorized_keys and save(ctrl + x, y, Enter)
   * Set permissions for files: chmod 700 .ssh chmod 644 .ssh/authorized_keys
   * Change PasswordAuthentication again to no and save file.
   * login with key pair: ssh grader@Public-IP-Address* -p 2200 -i ~/.ssh/project5
    
* Configure the Uncomplicated Firewall (UFW)
  * allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
  * sudo ufw status to check UFW status is inactive
  * Deny all incoming by defaultsudo ufw default deny incoming
  * Allow outgoing by default sudo ufw default allow outgoing
  * Allow SSH sudo ufw allow ssh
  * Allow SSH on port 2200 sudo ufw allow 2200/tcp
  * Allow HTTP on port 80 sudo ufw allow 80/tcp
  * Allow NTP on port 123 sudo ufw allow 123/udp
  * Turn the firewallsudo ufw enable
  
* configure the local timezone to UTC
  * sudo dpkg-reconfigure tzdata and select UTC
  
* Install and configure Apache to serve a Python mod_wsgi application
  * sudo apt-get install apache2 and check the working with your public IP givien during setup.
  * install mod_wsgi: sudo apt-get install libapache2-mod-wsgi 
  * sudo nano /etc/apache2/sites-enabled/000-default.conf to configure apache for handling WSGI module
  * add WSGIScriptAlias / /var/www/html/myapp.wsgi before </VirtualHost> closing line and save and restart apache by typing sudo apache2ctl restart 
  
* Install git, clone and setup your Item-Catalog project (from your GitHub repository from earlier in the Nanodegree program) 
so that it functions correctly when visiting your server’s IP address in a browser.
Remember to set this up appropriately so that your .git directory is not publicly accessible via a browser! and insatall git by typing sudo apt-get insatll git
 
* Install python dev
  * verify WSGI is enabled if not enable it
  * Install python-dev package sudo apt-get install python-dev
  * Verify wsgi is enabled sudo a2enmod wsgi

* Create flask app
  * Commands:
    * cd /var/www
    * sudo mkdir catalog
    * cd catalog
    * sudo mkdir catalog
    * cd catalog
    * sudo mkdir static templates
    * sudo nano __init__.py
    `from flask import Flask
    app = Flask(__name__)
    @app.route("/")
    def hello():
        return "Hello, world (This is Sidharths Project!)"
    if __name__ == "__main__":
    app.run()`
    
* Install flask
  * sudo apt-get install python-pip
  * sudo pip install virtualenv
  * sudo virtualenv venv
  * sudo chmod -R 777 venv
  * source venv/bin/activate
  * pip install Flask
  * python __init__.py
  * deactivate

* Configure New virtual host
  * Now create and enable new virtual host
  * Creating host config file sudo nano /etc/apache2/sites-available/catalog.conf
  * Paste the following in that:
   ```
    <VirtualHost *:80>
      ServerName 34.201.114.178
      ServerAdmin admin@34.201.114.178
      WSGIScriptAlias / /var/www/catalog/catalog.wsgi
      <Directory /var/www/catalog/catalog/>
          Order allow,deny
          Allow from all
      </Directory>
      Alias /static /var/www/catalog/catalog/static
      <Directory /var/www/catalog/catalog/static/>
          Order allow,deny
          Allow from all
      </Directory>
      ErrorLog ${APACHE_LOG_DIR}/error.log
      LogLevel warn
      CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
```

* Clone Github Item catalog app
  * sudo git clone https://github.com/sid2111995/item-catalog
  * shopt -s dotglob to view hidden files . Move files from clone directory to catalog 
  * remove cloned directory sudo rm -r devpost
 
* make .git inaccessible
  * from cd /var/www/catalog/ create .htaccess file sudo nano .htaccess
  * paste in RedirectMatch 404 /\.git
  * save file(nano: ctrl+x, Y, Enter)
  * for dependencies:
    * source venv/bin/activate
    * pip install httplib2
    * pip install requests
    * sudo pip install --upgrade oauth2client
    * sudo pip install sqlalchemy
    * pip install Flask-SQLAlchemy
    * sudo pip install python-psycopg2
    * in case of any other packages install them
    
* Install and configure Postgresql
  * install by typing sudo apt-get install postgresql
  * sudo apt-get install postgresql-contrib
  * config database_setup.py sudo nano database_setup.py
  * python engine = create_engine('postgresql://catalog:12345@localhost/catalog')
  * repeat for project.py
  * copy your main project.py file into the init.py file mv project.py __init__.py
  * sudo adduser catalog to add a catalog user
  * login as postgres super usersudo su - postgres
  * psql to enter into postgres
 

