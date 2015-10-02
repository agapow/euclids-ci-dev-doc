Hardening the server
====================
Server configuration for security and stability
-----------------------------------------------


Enable termination protection
-----------------------------

In the AWS console, context-click on the instance in question, select “Change termination protection” and enable it in the following dialog. This means that the instance cannot be terminated unless termination protection is un-checked using the same procedure.


Customize the server security group
-----------------------------------

The security group is essentially an external firewall to your server(s), showing whether traffic is allowed in and out on given ports. A security group can be shared by more than one server, allowing consistency.

You can find out what security group is being used by a context-click on the the instance. Security groups have their own list, which can be accessed in the left-hand pane.

To modify the group, select the group and under “actions” select “edit inbound (or outbound) rules”. Restricting ports is a tricky problem: the server could expect traffic or admin from several locations. The minimum we need is to allow SSH and HTTP in (ports 22 and 80). If the db is later migrated out to a seperate instance, that will have to be handled here too.


Create alarms and an alerts channel
-----------------------------------

Various alerts or diagnostic messages can be raised by AWS systems. Create a "topic" (channel) in the Amazon SNS (Simple Notification Service). Subscribe an email address to this channel.

# TODO: get a shared email address for project admin


Disable the root account
------------------------

In a sense, this has already been done. The Amazon Linux AMIs do not allow logging into the root account, but require you to log in as the ec2-user


Greylisting suspicious connections
----------------------------------

fail2ban is a utility that will deny IPs that try to connect too many times in a set period, thus warding off brute force and dictionary attacks. Install with::

	% sudo yum instal fail2ban

You may also install gamin for monitoring file changes::

     % sudo yum install gamin

Copy and edit the configuration file::

	% cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
	% vi /etc/fail2ban/jail.local

The “local” file overrides the main conf.

Most of the default settings are fine:

* Increase the bantime, findtime (period in which retries are allowed) and lower maxretry is desired
* Edit the ssh port if you have changed it
* Specific IPs or domains can be white-listed
* Sub-“jails” are provided for individuals applications are can be configured

Restart fail2ban with::

	% service fail2ban restart

Check that fail2ban is in place with::

	% sudo iptables -L

Note: while fail2ban documentation refers to setting a single alert email address, it actually seems to be a case of setting it in multiple places. IN any event, an alert address may be overwhelmed by email.

.. note:: Also, sometimes in a crash situation, fail2ban will leave behind a lock file that prevents it from restarting.


Deny suspicious hosts
---------------------

Denyhosts is a package that alllows the sharing of lists of bad hosts, so these can be denied connections. 

Follow the installation guide `here <https://survivalguides.wordpress.com/tag/amazon-ec2/>`__

There is also a nice "pre-cooked" lists of addresses to deny that originate from suspicious locales:  http://szarka.net/technotes/easysshdfilters.php




Lock the root password
----------------------

As it stands, a root user cannot login in. This is just a bit of extra security::

     % sudo passwd -l root


Check for UID 0 accounts
------------------------

Only the root should::

	% awk -F: '($3 == "0") {print}' /etc/passwd


Enable execshield and check for spoofing
----------------------------------------

Edit /etc/syscntrl and add::

	# turn on exec shield
	kernel.exec-shield=1
	kernel.randomize_va_space=1

	# protect against spoofing and broadcasts
	net.ipv4.conf.all.rp_filter=1
	net.ipv4.conf.all.accept_source_route=0
	net.ipv4.icmp_echo_ignore_broadcasts=1
	net.ipv4.icmp_ignore_bogus_error_messages=1
	net.ipv4.conf.all.log_martians = 1

See: https://forums.aws.amazon.com/thread.jspa?threadID=102232

Swicth off IPV6::

	% chkconfig ip6tables off


Harden Apache
-------------

Add to this /etc/httpd/conf.d/httpd.conf to prevent the swerver software from identifying itself::

	ServerSignature Off
	ServerTokens Prod

Disable (comment out) the loading of these modules::

	userdir, status, env, cgi, actions, include, filter, version, as-is, ldap, authnz_ldap_module


Check for problems
------------------

After making modifications, check for problems:

Check for accounts with empty passwords::

	% awk -F: '($2 == "") {print}' /etc/shadow

Check if only root account have UID set to 0::

	% awk -F: '($3 == "0") {print}' /etc/passwd
	
Check open ports::

	% netstat -tulpn
	
Check world writable files::

	% find / -xdev -type d \( -perm -0002 -a ! -perm -1000 \) -print
	
Check for no owner files::

	% find / -xdev \( -nouser -o -nogroup \) -print


Secure SSH
-----------

A simple step to securing SSH-bound attacks is simply changing the portal that SSH comes in on. While port scanners could establish what ports are active (although maybe the Amazon security policy prevents port scanning?), this deflects the script kiddies and has been shown to massively reduce attacks. 

Edit the security policy to allow the new port.

Edit /etc/ssh/sshd_config and change the port number::

	Port 811                    # the port number
	IgnoreRhosts yes            # rsh emulation
	PermitRootLogin  no         # disallow any root login
	HostbasedAuthentication no

.. note:: we choose a low and privileged port. 811 is supposedly unassigned, although some Mac program uses it sometimes.

.. note:: It may also be useful to set "UseDNS no" to get rid of the many spurious "POSSIBLE BREAKIN ATTEMPT message" that appear in logs.

Restart the SSH daemon::

	% service restart sshd
	
Check what ports are open. Amazon images seem to ship with most open::

	% netstat -a

Logout and attempt to login on the new port::

	% ssh -p 811 -i euclids.pem ec2-user@euclids-ci.eu
	
If successful, close the old SSH port in the security policy.


Setup daily updates
-------------------

For best effect, security updates (and perhaps all updates) should be applied regularly. Install::

	% yum install yum-cron-security

Edit the config file::

   % vi /etc/yum/yum-cron-security.conf

Switch updates on::

   apply_updates = yes
   
``/etc/yum/yum-cron-security.conf`` can be edited instead to just allow security updates.
   
Now switch the service on::

   % chkconfig yum-cron on
   
 and check that it is running::

   % chkconfig --list yum-cron


.. note:: there is the chance that this will break something but it seems worth the risk.



See: 

* https://fedoraproject.org/wiki/AutoUpdates

* https://linuxaria.com/pills/enabling-automatic-updates-in-centos-6-and-red-hat-6

* http://www.superb.net/kb/index.php/software-a-os-management/34-updates-a-patches/165-how-do-i-automatically-update-redhat-centos-or-fedora-daily


Create a backup admin user
--------------------------

::

     % sudo adduser euclids_admin
     % sudo passwd euclids_admin

The password is “far60+OWN”(fargoTOWN)

Now you have to create a pem file, which is basically the private key of a public-private key pair.

You can remove this user with::

     % userdel euclids_admin

# TODO:
* Reconfiguring shared memory
* Set locale
* Mysql secure
* backing up
