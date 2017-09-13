# lightsail-server-setup
Details for the configuration of a Virtual Private Server (Making it private)

<a href="https://www.ubuntu.com/"><img src="http://icons.iconarchive.com/icons/martz90/circle/48/ubuntu-icon.png" width="50" height="50" alt="Ubuntu logo"></a> <a href="https://www.apache.org/"><img src="https://www.apache.org/foundation/press/kit/poweredBy/Apache_PoweredBy.png" alt="Apache logo" width="50" height="50"></a> 
<a href="https://aws.amazon.com/"><img src="http://icons.iconarchive.com/icons/designbolts/handstitch-social/256/Aws-icon.png" alt="Amazon logo" width="50" height="50"></a>

**Public IP:** 34.210.213.40
**Port:** 2200
**Url:** http://ec2-34-210-213-40.us-west-2.compute.amazonaws.com

In order to deploy your application, you will need to configure your Lightsail VPS and secure it. 
## Login 
Launch the VPS either from the browser, within Lightrail, or via your own terminal **(Recommended)**

## Update the software

**Update**

```$ sudo apt-get update```
<br/>

**Upgrade**

```$ sudo apt-get upgrade```


## Create a New User and Give it Sudo Access

```$ sudo adduser {newuser}```

CD into the sudoers.d directory
<br/>
```$ cd /etc/sudoers.d```

Then create a new file named after the user you have created
<br/>
```$ sudo vim {newuser}```

Add the following to this new file 
<br/>
```{newuser} ALL=(ALL:ALL) ALL```

## Set SSH Authentication

On your local machine, generate a new SSH key with ssh-keygen
<br/>
```ssh-keygen```
save this in the .ssh file on your local machine.

Back on your the VPS, change to the new user 
<br/>
```$ su - {newuser}```

Make a new directory, .ssh, and add the authorized_keys file **(Pro tip: Authorized NOT Authorised)** 👌
<br/>
```$ mkdir .ssh```
<br/>
```$ touch .ssh/authorized_keys```
<br/>
```$ vim .ssh/authorized_keys```

Then set permissions with the Chmod command
<br/>
```$ chmod 700 .ssh```  _owner can read, write and execute_
<br/>

```$ chmod 644 .ssh/authorized_keys```  _owner can read, write_ 

Navigate to the sshd_config file and open with VIM or Nano
<br/>
```$ sudo vim /etc/ssh/sshd_config```
Find the line PasswordAuthentication and change it from **yes** to **no**

Restart to run the new configurations
<br/>
```$ sudo ssh restart```

Now you can login as your new user with ssh
<br/>
```ssh -i ~/.ssh/{private-key-filename} {newuser}@52.24.125.52```

## Configure your firewall

```$ sudo ufc default deny incomming```
<br/>
```$ sudo ufw default allow outgoing```

## Configure the Ports 

Set the Firewall to only allow incoming connections for SSH port 2200, NTP port 123 and HTTP port 80
<br/>
```$ sudo ufw allow 2200/tcp```
<br/>
```$ sudo ufw allow 80/tcp```
<br/>
```$ sudo ufw allow 123/udp```
<br/>
```$ sudo ufw enable ```

## Confirm Timezone is Set to UTC
```$ sudo dpkg-reconfigure tzdata```

## Install and configure Apache2
```$ sudo apt-get install apache2```
Confirm is is working by going to localhost:8080 in your browser
Install mod_wsgi
<br/>
```$ sudo apt-get install libapache2-mod-wsgi```


Edit the 000-default-file
<br/>
```$ sudo vim /etc/apache2/sites-enabled/000-default.conf```

## Install and configure Postgresql
```$ sudo apt-get install postgresql```
Ensure remote connections are not authorised
<br/>
```$ sudo vim /etc/postgresql/9.5/main/pg_hba.conf```
local postgres should be set to **md5** not **peer**

Change to the postgres user
<br/>
```$ sudo su - postgres```
<br/>
run psql and create a new user and database
<br/>
```$ psql```
<br/>
```$ postgres=# CREATE DATABASE catalog```
<br/>
```$ postgres=# CREATE USER catalog WITH PASSWORD '{password}```
<br/>
Give the user permission to access the database
<br/>
```$ postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog```
<br/>
quit psql and exit the postgres user

## Install Git and Clone your Catalouge Project: [Catalog](https://github.com/RoseannaM/catalog)
```$ sudo apt-get install git```
<br/>
Go to the www directory
<br/>
```$ cd /var/www```
<br/>
Clone to project
<br/>
```$ sudo git clone https://github.com/RoseannaM/catalog```
<br/>
Rename it to FlaskApp
<br/>
```$ sudo mv ./catalog ./FlaskApp```
<br/>
```$ cd FlaskApp```
<br/>
Rename main to __init__
<br/>
```$ sudo mv main.py __init__.py```
<br/>
Update the engine path in all the files that use it to the below
<br/>
```python
engine = create_engine('postgresql://catalog:{password}@localhost/catalog')
```

Install Pip and the dependencies
```$ sudo apt-get install python-pip```
<br/>
```$ sudo pip install -r requirements.txt```

Create the database
<br/>
```$ sudo python database.py````
<br/>

## Create a new Virtual Host: [Walkthrough to Setting up Virual Host ](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
Edit the FlaskApp.conf fille
<br/>
```$ sudo nano /etc/apache2/sites-available/FlaskApp.conf```
<br/>
Paste the below

```
<VirtualHost *:80>
        ServerName 34.210.213.40
        ServerAlias ec2-34-210-213-40.us-west-2.compute.amazonaws.com
        ServerAdmin admin@gmail.com
        WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
        <Directory /var/www/FlaskApp/>
                Order allow,deny
                Allow from all
        </Directory>
        Alias /static /var/www/FlaskApp/static
        <Directory /var/www/FlaskApp/static/>
                Order allow,deny
                Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Restart the server
<br/>
```$ sudo apache2ctl restart```

