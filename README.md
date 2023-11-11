# Guide-to-Install-Frappe-ERPNext15-in-Ubuntu-22.04-LTS
A complete Guide to Install Frappe Bench in Ubuntu 22.04 LTS and install Frappe/ERPNext Application

### Pre-requisites 
```
Python 3.10+
Node.js 18
Redis                                         (caching and real time updates)
MariaDB 10.6.x                                (to run database driven apps)
yarn 1.12+                                    (js dependency manager)
pip 22+                                       (py dependency manager)
wkhtmltopdf (version 0.12.6 with patched qt)  (for pdf generation)
cron                                          (bench's scheduled jobs: automated certificate renewal, scheduled backups)
NGINX                                         (proxying multitenant sites in production)
```
### STEP 1 Update Server
It is always a good idea to upgrade the Ubuntu package if anything is available, run the below command to upgrade and update.
```    
sudo apt update && sudo apt upgrade -y
```
Check the server's current timezone
```
date
```    
Set correct timezone as per your region
```
timedatectl set-timezone "Asia/Kolkata"
```
You can run the following command to select your timezone.
```
timedatectl list-timezones
```    
It is always recommended to reboot the server once the upgrade is done.
```
sudo reboot
```
### STEP 2  Create a new user
I am using **erpnext** as frappe-user

#### you should not use _frappe_ or _erpnext_ as a frappe-user on your production server.
```
sudo adduser erpnext
sudo usermod -aG sudo erpnext
su - erpnext
cd /home/erpnext/
```
### STEP 3 Install git
Git is the most commonly used version control system. Git tracks the changes you make to files, so you have a record of what has been done, and you can revert to specific versions should you ever need to. Git also makes collaboration easier, allowing changes by multiple people to all be merged into one source.
```    
sudo apt install git -y
```
### STEP 4 Install python3-dev and python3-venv
Python-dev is the package that contains the header files for the Python C API, which is used by lxml because it includes Python C extensions for high performance.
```
sudo apt install python3-dev python3.10-dev python3-venv -y
```
### STEP 5 Install setuptools, pip and etc. (Python's Package Manager).
Setuptools is a collection of enhancements to the Python distutils, allowing developers to more easily build and distribute Python packages, especially those that have dependencies on other packages. Packages built and distributed using setuptools appear to the user as ordinary Python packages based on the distutils.

Pip is a package manager for Python. It's a tool that allows you to install and manage additional libraries and dependencies that are not distributed as part of the standard library.

Rest are the weasyprint dependencies.
```
sudo apt install python3-setuptools python3-pip python3-distutils python3-wheel python3-cffi libcairo2 libpango1.0-0 libpangocairo-1.0-0 libgdk-pixbuf2.0-0 libffi-dev shared-mime-info -y
```
### STEP 6 Install Redis server
Redis can be used to process and analyze data in memory, this is prerequisite for ERPNext.
```
sudo apt install redis-server -y
```
### STEP 7 Install software-properties-common
Now install the below package to manage the repository, usually, Ubuntu 22.04 has already installed it, but for the safe side, we will run this command.
```
sudo apt install software-properties-common -y
```
If prompt for "Override local changes to /etc/pam.d/common-*?" on PAM Configuration, then safely choose "No".

### STEP 8 install wkhtmltopdf
Wkhtmltopdf is an open source simple and much effective command-line shell utility that enables user to convert any given HTML (Web Page) to PDF document or an image (jpg, png, etc).
```
sudo apt install xvfb libfontconfig1 fontconfig libxrender1 xfonts-75dpi wkhtmltopdf -y
```
<!--    sudo reboot

after reboot

    sudo su - erpnext
-->

### STEP 9 Install MariaDB
```
sudo apt install mariadb-server mariadb-client -y
```
<!--
IMPORTANT: During this installation you'll be prompted to set the MySQL root password.
If you are not prompted for the same You can initialize the MySQL server setup by executing the following command.

sudo service mysql start       #run this command first, only if you are running Ubuntu in a Virtual Machine.    
-->
```
sudo mysql_secure_installation
```
#### Prompt
``` 
Enter current password for root (enter for none):   (safely press Enter)
Switch to unix_socket authentication [Y/n]          (Press "Y")
Change the root password? [Y/n]                     (Press "Y")
New password:                                       ("Enter new Password")
Re-enter new password:                              ("Re-enter new Password")
Remove anonymous users? [Y/n]                       (Press "Y")
Disallow root login remotely? [Y/n]                 (Press "Y") //If Press "N" then You want to access the database from a remote server for using business analytics software like Metabase / PowerBI / Tableau, etc.
Remove test database and access to it? [Y/n]        (Press "Y")
Reload privilege tables now? [Y/n]                  (Press "Y")
```

### STEP 10  MySQL database development files
```
sudo apt install libmysqlclient-dev -y
```
### STEP 11 Edit the mariadb configuration (unicode character encoding)
You need to ensure to change the default character set of MySQL or MariaDB to Unicode instead of general. To do this you will need to edit the maria DB configuration file which is in this version located at /etc/mysql/mariadb.conf.d directory so you can directly edit this or locate the folder and then edit the file by typing the below command.
```
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```
Once the file opens you need to locate the line where collation-server says general and you need to modify it to Unicode as below,
```
collation-server = utf8mb4_general_ci
```
Modify above as below.
```
collation-server = utf8mb4_unicode_ci
```
Now press (Ctrl-X) and Save then exit.

And also locate my.cnf and edit the below configuration.
```
sudo nano /etc/mysql/my.cnf
```
Make sure your configuration has the below lines in the file.
```
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[mysql]
default-character-set = utf8mb4
```
Now press (Ctrl-X) and Save then exit.

Now MySQL or MariaDB setup is now ready, let us now restart this service. You can alternatively reboot as well.
```
sudo service mysql restart
```
### STEP 12 install Node.js package
```
sudo apt install curl
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
source ~/.profile
nvm install 18
```
<!--
Sometimes, due to github raw content server not responding, you have to manually download "install.sh" and run in terminal.
Download from **"https://github.com/nvm-sh/nvm/archive/refs/tags/v0.39.5.zip"**
Then unzip and copy/paste "install.sh" file in your frappe-user home directory.
Then run
```
bash install.sh
source ~/.profile
nvm install 18
```
-->
Now it has been installed, you can now check the version by typing the below command.
```
node -v
```
### STEP 13 Install Yarn using NPM
Now we will install Yarn which is a software packaging system developed by Facebook for Node.js, this is open source, so we will install it using npm.
```
npm install -g yarn
```
Now our server is ready for the installation of the frappe environment, let us now dive into the frappe environment installation.

### STEP 14 Bench Installation
The bench is a Command-line tool to manage Frappe Deployments, this tool has various commands, the frappe uses the bench for the command-line tool as well as for the bench directory, so don’t confuse yourself. We will first install the bench package which will be used to set up a frappe environment, create a site, do backup, change the setup, and so on. In short, Bench provides a user-friendly interface to set up and manage multiple frappe-based applications and sites where erpnext is one of the applications.

Now let us install the bench
```
sudo -H pip install frappe-bench
```
It will install a bench and will give you a message that the bench is installed successfully, now you can use various bench commands. Starting with the command "bench".

### STEP 15 Install Frappe-Bench Environment using bench CLI
Let us now create the frappe-bench environment. Here you have to decide the purpose for which you are installing ERPNext, it is just for test or training then you can use the latest version, which will be developing and may not be stable. However you can also use a stable version by choosing a specific version, You can search and learn which is the stable version today.
    
To choose a specific stable version (for Production) you can use the branch version. I will be using branch version 14 in this installation. You can look for the latest stable release of the frappe environment.
```
bench init frappe-bench --verbose --frappe-branch version-14
```
<!-- To deploy the latest (for development) frappe-bench environment make sure to run the command while you are in your home directory or your user and use the below command.

    bench init frappe-bench
-->
Now frappe bench environment is installed using bench CLI.

Now you can use various bench commands by changing the directory. So you can need to change the directory.
```
cd frappe-bench
```
Once you type "bench" you will see the various commands that bench cli has. Don’t worry, we will not be using all the commands, we just need to install EPRNext but have a quick look at these commands.

### STEP 16 ERPNext Installation on Frappe Environment

make sure that your working directory is frappe-bench.

Now you need to get the app from the frappe repository, have two options, either clone the latest development package, which is not stable currently, or install the stable package. I have given both options for you to choose from.

<!-- To install the current package which is not stable you can use the below command

    bench get-app erpnext https://github.com/frappe/erpnext
-->
Frappe and ERPNext version would be same for a proper installation. We will be installing ERPNext Version 14, for that, We will be using the below command.
```
bench get-app erpnext --branch version-14
```
Payments app is mandatory from ERPNext version-14.
```
bench get-app payments --branch version-14
```
From any of the options, it will clone the next application into the app’s directory of the frappe-bench directory. You don’t need to do anything with the directories. Just ensure that erpnext is available in the directory.

Now you have the application installed in your environment. The next step is to install the application on-site, but before that, we need to create a new site.

##### Run ```bench start``` then open another terminal window, then go to the "frappe-bench" directory and start from there

 <!--   bench start -->

To create a site, we will use the bench command as below
```
bench new-site erp.YOURDOMAIN.COM 
```
Now site is deployed, by default frappe application will be installed at site. Don’t open the site yet, because we need to install ERPNext to the site.

#### Make sure your sub-domain properly configured to access this server, where you hosted your domain.

If you have not created subdomain, then create a subdomain (preferred) erp.YOURDOMAIN.COM. Then Change DNS records as below:
```
Hostname                Type    Value               TTL
erp.YOURDOMAIN.COM      A       ERPNext-Server-IP   1800
www.erp.YOURDOMAIN.COM  A       ERPNext-Server-IP   1800
```
Run the below command to install ERPNext to the site that you have recently created
```
bench --site erp.YOURDOMAIN.COM install-app erpnext
```
it will take a few moments to install the erpnext application on your site.

Now as we created a new site, we need to make sure this is our default site, so we have to tell bench to use this site as default by using the below command
```
bench use erp.YOURDOMAIN.COM
bench start
```
Now ERPNext is installed on your server and you are ready to configure it. But before configuring there are few more steps in case you want to use this for production.

<!--
### STEP ## ERPNext Setup for Production

ERPNext only supports NGINX, so you can't use apache2 on this server. You have to remove Apache2 from your server.

sudo apt-get remove --purge apache2 apache2-data apache2-utils apache2-bin apache2.2-common

You can do the following two tests to confirm apache has been removed:

Run

which apache2

This should return a blank line.

Run

sudo service apache2 start

This should return apache2: unrecognized service
-->

### STEP 17 Production Deployment
We will use an automatic bench set up for production by using the below command.

#### Automatic Method:
run two times
```
sudo bench setup production erpnext
```
#### Manual Method:
##### Setup Bench Supervisor
In case the supervisor is not installed you can use the below command
```
sudo apt -y install supervisor
bench setup supervisor
sudo ln -s `pwd`/config/supervisor.conf /etc/supervisor/conf.d/frappe-bench.conf
```
##### Setup Bench NginX
```
bench setup nginx
sudo ln -s `pwd`/config/nginx.conf /etc/nginx/conf.d/frappe-bench.conf
```
Now you will get a message saying that erp.YOURDOMAIN.COM is on port 80

You can simply open erp.YOURDOMAIN.COM in your web browser and check it will work fine.

### STEP 18 ERPNext SSL Installation NginX

Now your site is ready, you must configure the SSL certificate, I have explained that in simple steps.

First, we will install spanny package as below
```
bench config dns_multitenant on
sudo pip install -U cryptography
sudo pip install certbot
sudo bench setup lets-encrypt erp.YOURDOMAIN.COM
```

#### if getting error like "Some challenges have failed.", then open port 80 and 443 or try below commands
```
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 443 -j ACCEPT
sudo netfilter-persistent save
sudo systemctl restart nginx
```

### STEP 19 If getting [Error 13] on bench restart (Optional)

#### error: <class 'PermissionError'>, [Errno 13] Permission denied: file: /usr/lib/python3/dist-packages/supervisor/xmlrpc.py line: 560
```
sudo addgroup supervisor
sudo usermod -aG supervisor erpnext
```
After logging-out/logging-in (so that the new group membership takes effect), edit the supervisord configuration file (/etc/supervisor/supervisord.conf) to make the unix_http_server section look as follows
```
sudo nano /etc/supervisor/supervisord.conf
```

_[unix_http_server] \
file=/var/run/supervisor.sock ; (the path to the socket file) \
**chmod=0760** ; socket file mode (default 0700) \
**chown=erpnext:erpnext**_

```
sudo service supervisor restart
sudo supervisorctl stop all
sudo bench setup production erpnext
```    
### STEP 20 If _js_ and _css_ files are not loading on login window, run the following command (Optional)
```
sudo chmod o+x /home/erpnext
```
#### Optional Modules (open new terminal):
```
bench get-app --branch version-14 india_compliance https://github.com/resilient-tech/india-compliance
bench --site erp.YOURDOMAIN.COM install-app india_compliance

bench get-app hrms --branch version-14
bench --site erp.YOURDOMAIN.COM install-app hrms

bench get-app chat
bench --site erp.YOURDOMAIN.COM install-app chat

bench get-app desk_navbar_extended https://github.com/gavindsouza/desk-navbar-extended
bench --site erp.YOURDOMAIN.COM install-app desk_navbar_extended

bench get-app persona https://github.com/iptelephony/persona
bench --site erp.YOURDOMAIN.COM install-app persona

bench get-app print_designer
bench --site erp.YOURDOMAIN.COM install-app print_designer

bench get-app website_builder
bench --site erp.YOURDOMAIN.COM install-app website_builder

bench get-app --branch version-14 it_management https://github.com/phamos-eu/it_management
bench --site erp.YOURDOMAIN.COM install-app it_management

bench get-app raven https://github.com/The-Commit-Company/Raven
bench --site erp.YOURDOMAIN.COM install-app raven

bench get-app helpdesk
bench --site erp.YOURDOMAIN.COM install-app frappedesk #(Not recommended on same site)

bench get-app insights
bench --site erp.YOURDOMAIN.COM install-app insights #(Not recommended on same site)

bench get-app drive
bench --site erp.YOURDOMAIN.COM install-app drive #(Not recommended on same site)
```

<p align="center">
  <b>Thank You!🙏</b>
</p>

