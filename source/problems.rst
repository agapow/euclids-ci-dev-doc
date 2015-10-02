Problems
========


SSH fails because acccount is locked
------------------------------------

Symptoms
~~~~~~~~

You attempt to log in with SSH and are bounced with the message::

	Permission denied (publickey)

The key or password you are using is undeniably correct.

If you get into the system and look at ``/var/log/secure`` the reason given is::

	User ec2-user not allowed because account is locked
	input_userauth_request: invalid user ec2-user [preauth]

Using passwd to exmaine the account shows it is identical to the functional account on another system::

	% passwd -S ec2-user
	ec2-user LK 2014-07-24 0 99999 7 -1 (Password locked.)
	
Looking directly in the password tells the same story.
	
	
Explanation
~~~~~~~~~~~

OpenSSH checks for locked accounts by default. Normally this isn't a problem except if you disable the PAM check in sshd_config (as some SFTP setup guides suggest), sshd looks at the password file ``/etc/shadow``, sees your password is locked and hollers.

Note that most explanations of this problem on the web home in on the password issue or dwell into generic SSH debugging, missing the behaviour that triggered the change.


Solution
~~~~~~~~

Enable PAM in ``sshd_config``::

	UsePAM yes

Restart the sshd daemon.


References
~~~~~~~~~~

* https://www.rodneybeede.com/How_OpenSSH_checks_for_locked_Linux_accounts.html


::

	>pwd
	/afs/slac.stanford.edu/u/sf/teresa

	>cat > .forward

	teresa@slac.stanford.edu

	>cat .forward

	teresa@slac.stanford.edu
 
	>chmod 644 .forward




