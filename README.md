# lightsail-server-setup
Details for the configuration of a Virtual Private Server (Making it private)

<a href="https://www.ubuntu.com/"><img src="http://icons.iconarchive.com/icons/martz90/circle/48/ubuntu-icon.png" width="50" height="50" alt="Ubuntu logo"></a> <a href="https://www.apache.org/"><img src="https://www.apache.org/foundation/press/kit/poweredBy/Apache_PoweredBy.png" alt="Apache logo" width="50" height="50"></a> 
<a href="https://aws.amazon.com/"><img src="http://icons.iconarchive.com/icons/designbolts/handstitch-social/256/Aws-icon.png" alt="Amazon logo" width="50" height="50"></a>

**Public IP:** 4.210.213.40
**Port:** 2200
**Url:** http://ec2-34-210-213-40.us-west-2.compute.amazonaws.com/toystores/

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




i. The IP address and SSH port so your server can be accessed by the reviewer.
ii. The complete URL to your hosted web application.
iii. A summary of software you installed and configuration changes made.
iv. A list of any third-party resources you made use of to complete this project.
Locate the SSH key you created for the grader user.
During the submission process, paste the contents of the grader user's SSH key into the "Notes to Reviewer" field.
