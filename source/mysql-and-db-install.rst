Database installation
=====================

MySql
-----

# NOTE: php installation mixed in

Installing MySQl is not complicated but follow these steps::

	% sudo yum groupinstall -y "Web Server" "MySQL Database" "PHP Support"
	% sudo yum install -y php-mysql

	% sudo service httpd start
	% sudo chkconfig httpd on
	% chkconfig --list httpd

	% sudo groupadd www
	% sudo usermod -a -G www ec2-user
	% exit

Then login::

	% sudo chown -R root:www /var/www
	% sudo chmod 2775 /var/www
	% find /var/www -type d -exec sudo chmod 2775 {} +
	% find /var/www -type f -exec sudo chmod 0664 {} +

	% echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php
	% rm /var/www/html/phpinfo.php

	% sudo service mysqld start
	% sudo mysql_secure_installation
	Set root password? [Y/n] Y
	Remove anonymous users? [Y/n] 
	Disallow root login remotely? [Y/n] 
	Remove test database and access to it? [Y/n] 
	Reload privilege tables now? [Y/n] 

PW is basquet

	% sudo chkconfig mysqld on
	% sudo yum --enablerepo=epel install phpmyadmin
	% sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
	% yum erase phpmyadmin

phpMyAdmin is required for REDCAp install, but may be worked around


RDS
---

# TODO