Installing PHP on EC2
=====================

See: http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-LAMP.html

This assumes an amazon instance. Details will vary for other OSes.

#. Check everything is up to date::

   % sudo yum update -y
   
#. Mass-install all the things::

   % sudo yum groupinstall -y "Web Server" "MySQL Database" "PHP Support"
   
   
PHP
---
   
#. Install PHP::

   % sudo yum install -y php-mysql
   
#. Start the web serving, create a service to do that and check it::

   % sudo service httpd start
   % sudo chkconfig httpd on
   
#. Surf to the site address and you should see the Apache test page.

#. The web directory root is usually '/var/www' and it is usually owned by root. Change this. You may have to log out and in again::

   % sudo groupadd www
   % sudo usermod -a -G www ec2-user
   % exit
   % (login)
   % groups
   % sudo chown -R root:www /var/www
   % sudo chmod 2775 /var/www
   % find /var/www -type d -exec sudo chmod 2775 {} +
   % find /var/www -type f -exec sudo chmod 0664 {} +

#. Create a simple php file to test everything::

   % echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php
   
#. Surf to your website/phpinfo.php and you should see a PHP information page.

MySQL
-----

#. Mysql is already installed. You just have to configure it::

   % sudo service mysqld start
   % sudo mysql_secure_installation
   
The current password for root will be blank. Get the root users a new password (basquet)

Type Y to remove the anonymous user accounts.

Type Y to disable remote root login.

Type Y to remove the test database.

Type Y to reload the privilege tables and save your changes.

#. Setup MySQL to run at boot::

   % sudo chkconfig mysqld on


PhpAdmin
--------

The Amazon repos don't contain phpAdmin, so you have to install it manually.

See:

http://calebogden.com/wordpress-on-linux-in-the-amazon-cloud-with-mac/

Download PhpAdmin from its website::

   % wget http://sourceforge.net/projects/phpmyadmin/files/phpMyAdmin/3.3.9.1/phpMyAdmin-3.3.9.1-all-languages.tar.gz
   
Install into the root of the web folder::

   % tar zxvf 
   % cp -r 
   
Rename it to something useful::

   % mv phpMyAdmin-4.2.6-all-languages phpmyadmin
   
	sudo adduser phpmyadmin
	sudo passwd phpmyadmin  
	sudo chown -R phpmyadmin:apache phpmyadmin/

Password is basquet

Then configure PhpMyAdmin::

   cd /var/www/html/phpmyadmin
   mkdir config
   chmod o+rw config
   cp config.sample.inc.php config/config.inc.php
   chmod o+w config/config.inc.php
   
Restrat apache and browse your webite/phpadmin::

	% service httpd restart



Installing an SFTP server
-------------------------

From the notes at: https://survivalguides.wordpress.com/category/it-survival/amazon-aws/ec2/

Install VSFTP and start it::

	% yum install vsftp
	% service vsftpd start
	
Note that it will run over the SSH port. 

# TODO: since our main server is set up to not use passwords, trciky to set up sftp. Come back to it later. Alkso, the default state of VSFTPD is too open

# TODO: set up a seperate sftp server and share the volume?






