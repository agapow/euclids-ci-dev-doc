Modifying Linux
===============
Server configuration for performance, comfort and convienience
--------------------------------------------------------------


Set a host "nickname"
---------------------

Although one of the first things you do when setting up a server is usually set the hostname, there is `some argument for not doing with with EC2 <https://stackoverflow.com/questions/603351/can-we-set-easy-to-remember-hostnames-for-ec2-instances>`__. In short, while you can set it by editting `/etc/hostname`, many services seem to depend on it being set in a standard way.

You can find out the hostname(s) with::

	# e.g. ip-172-31-46-184
	% hostname
	# e.g. ip-172-31-46-184.eu-west-1.compute.internal
	% hostname --fqdn

However you can `set a host nickname <http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/set-hostname.html>`__, that will at least appear in the prompt to remind you where you are::

	% sudo sh -c 'echo "export NICKNAME=webserver" > /etc/profile.d/prompt.sh'

Edit `$NICKNAME` into an appropriate bash profile file.


Swap space
----------

Amazon micro instances come with no swap space configured by default. It makes sense to allocate swap space for when the the system is overwhelmed. Instead of providing another disk or partition, you can allocate a file:

Check the free memeory and swap space available with::

	% free -k
	% swapon -s
	
Setup the swap file with::

	% dd if=/dev/zero of=/swapfile bs=1M count=1024

This allocates 1024 blocks of a megabyte each. Now designate and enable the swap space::

	% mkswap /swapfile
	% swapon /swapfile 

To mount the swapfile at startup, add this line to `/etc/fstab`::

	/swapfile swap swap defaults 0 0

and test with::

	% mount -fav

"Ignore" results are okay.

Secure the swap file with::

	% chown root:root /swapfile 
	% chmod 0600 /swapfile

The "swappiness" can eb set to 0 (i.e. only use when out of memory) with::

	% echo 0 | sudo tee /proc/sys/vm/swappiness
	% echo vm.swappiness = 0 | sudo tee -a /etc/sysctl.conf

.. note:: You could use an external EBS volume. However, a memory-hungry app can generate a large amount of I/O requests, which are charged for on EBS. So it's better for the swap to be on the Instance (emphemeral) storage (ephemeral) disk and not an EBS device. EBS is also slower than the Instance Store and the Instance Store comes free with the EC2 Instance.

See:

* http://danielgriff.in/2014/add-swap-space-to-ec2-to-increase-performance-and-mitigate-failure/
* https://stackoverflow.com/questions/10284532/amazon-ec2-mysql-aborting-start-because-innodb-mmap-x-bytes-failed-errno-12

# TODO: set up another volume for swap space?


Delete unncessary software
--------------------------

AMI images tend to be suppliued fairly lean anyway. But X Windows is not required on a server. There is no reason to run X Windows on your dedicated mail and Apache web server. You can disable and remove X Windows to improve server security and performance. Edit /etc/inittab and set run level to 3. Finally, remove X Windows system, enter::

	% yum groupremove "X Window System"


