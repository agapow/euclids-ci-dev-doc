Peculiarities and odd facts about AWS
=====================================

Hostname
--------

The hostname is set by EC2 and cloud-init. While you can chnage it, it seems best not to. See elsewhere in these notes.


Credentials
-----------

When using credentials (a .pem file) to log into an instance, you may get the error message::

     @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
     @         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
     @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
     Permissions 0644 for 'Credentials/euclids-aws-credentials.csv' are too open.
     It is required that your private key files are NOT accessible by others.
     This private key will be ignored.
     bad permissions: ignore key: Credentials/euclids-aws-credentials.csv
     Permission denied (publickey).

Essentially, you can’t log in if the permission on the key file are accessible to others. Make them more restrictive with::

     chmod 400 Credentials/euclids-aws-credentials.csv

This makes your credentials only readable by the owner. Others suggest 600 (read-write).


Time out
--------

"Connection from Amazon EC2 timed out after 10001 milliseconds"

This is because you have no outgoing connectivity from the instance, probably because you have no outgoing rules in the security group.


Cloud-init
----------

cloud-init is the package that handles initialization of a cloud instance and is used by AWS to setup various things, including:

* setting a default locale
* setting hostname
* generate ssh private keys
* adding ssh keys to user's .ssh/authorized_keys for logging in

cloud-init's behavior can be configured via user-data. User-data can be given by the user at instance launch time. This is done via the --user-data or --user-data-file argument to ec2-run-instances


Jargon
------

An AWS snapshot is taken from a volume. An AMI makes and is made from a volume, but you can also add volumes to an AMI.


Services
--------

You can start a service (and set it up for automatic start)::

	% sudo chkconfig --add denyhosts
	% service denyhosts start

and remove it with::

	% chkconfig --del denyhosts


Miscellaneous
-------------

The AWS EC2 console has a “connect” command. This just tells you how to connect with SSH. You still need a .pem file.


	
	
	
	