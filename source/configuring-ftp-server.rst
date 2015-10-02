Configuring an SFTP server
==========================

::

	% yum install vsftpd
	% adduser uploader
	* sudo su
	* passwd uploader
	idG3pdUHEMiK
	su uploader
	[uploader@ftp_server ec2-user]$ cd /home/uploader/
	[uploader@ftp_server ~]$ ssh-keygen -b 1024 -f newkey -t dsa
	Generating public/private dsa key pair.
	Enter passphrase (empty for no passphrase): 
	Enter same passphrase again: 
	Your identification has been saved in newkey.
	Your public key has been saved in newkey.pub.
	The key fingerprint is:
	65:d7:06:bc:78:3e:f1:2e:c3:29:5f:6f:db:2d:c6:81 uploader@ip-172-31-37-50
	The key's randomart image is:
	+--[ DSA 1024]----+
	|           ..    |
	|            .o   |
	|          o...o  |
	|         o..+.   |
	|        S  o +   |
	|            E o  |
	|           . =.. |
	|          . =.=.+|
	|           o.+ +=|
	+-----------------+
	[uploader@ftp_server ~]$ mkdir .ssh
	You have new mail in /var/spool/mail/ec2-user
	[uploader@ftp_server ~]$ chmod 700 .ssh
	[uploader@ftp_server ~]$ cat newkey.pub > .ssh/authorized_keys
	[uploader@ftp_server ~]$ chmod 600 .ssh/authorized_keys 
	[uploader@ftp_server ~]$ exit
	exit
	You have new mail in /var/spool/mail/ec2-user
	[root@ ec2-user]# sudo chown uploader:ec2-user /home/uploader/.ssh/
	You have new mail in /var/spool/mail/ec2-user
	[root@ ec2-user]# sudo chown uploader:ec2-user /home/uploader/.ssh/authorized_keys 
	[root@ ec2-user]# cp /home/uploader/newkey
	newkey      newkey.pub  
	[root@ ec2-user]# cp /home/uploader/newkey* .
	[root@ ec2-user]# pwd

	sudo groupadd sftponly
	sudo usermod -a -G sftponly uploader
	id uploader



Creating and using volumes
---------------------------

See: http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html

Create a volume

Attach it to your instance

USe lsblk is see what volumes you have and what they are called

EBS volumes are raw when created you may have to format

::

	sudo file -s /dev/xvdf will say data if there is no file system
	sudo mkfs -t ext4 "device_name"
	sudo mkdir "mount_point"
	sudo mount /dev/xvdf /data
	sudo cp /etc/fstab /etc/fstab.orig
	sudo vi /etc/fstab
	/dev/xvdf       /data   ext4    defaults,nofail        0       2

nofail means system will boot wihtout this vol attached

::

	% sudo mount -a
http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-add-volume-to-instance.html
df -h