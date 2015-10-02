Adding more users
=================
When one is not enough
----------------------

Every extra account is an additional security risk and you could just use the generated ec2-user on each of your instances, but there's a few reasons why you might add more users:

* Just in case you lose the credentials for the ec2 user and want to avoid locking yourself out
* A second root user just for that machine, so you're not using the same user / .pem file to get into every instance
* Another user without root privileges for work that doesn't require it
* Another account in case the ec2 user is compromised

In balance, it seems a second account may often be worth the risk. 

.. note:: if you're locked out by IP, a second account is not going to help you.


The process
-----------

Based on:

* https://github.com/KartikTalwar/playground/blob/master/documentation/Web/Adding%20a%20user%20to%20Amazon%20EC2%20Server.md
* http://utkarshsengar.com/2011/01/manage-multiple-accounts-on-1-amazon-ec2-instance/
* http://brianflove.com/2013/06/18/add-new-sudo-user-to-ec2-ubuntu/


Log into the instance as the root or ec2 user. Create a new user and give them a password::

	% sudo adduser foo
	% sudo su
	% passwd foo

Assume the users identity and go to their home directory::

	% su ktalwar
	% cd /home/ktalwar

Generate a new key:: 

	% ssh-keygen -b 1024 -f foo -t dsa
	
This will create two files, the private and public keys or "foo" and "foo.pub". Copy the public key to their authorized keys::

	% mkdir .ssh
	% chmod 700 .ssh
	% cat foo.pub > .ssh/authorized_keys
	% chmod 600 .ssh/authorized_keys

Revert back to being the root user. Set the right ownership on their key and copy the private key back to your account::

	% sudo chown foo:ec2-user .ssh
	% sudo chown foo:ec2-user .ssh/authorized_keys
	% cp /home/foo/foo .

Copy the private key to your local machine by scp or sftp. Test it::

	% ssh -i foo foo@xx.xx.xxx.xxx

Clean up by naming the new key appropriately, saving it somewhere safe, deleting the keys in the ec2 and new user accounts::

	% rm /home/foo/foo
	% rm /home/foo/foo.pub
	% rm foo

.. important:: Test it, test it, test it.
