So, you've locked yourself out of your instance
===============================================
Help
----

Sooner or later, you're going to somehow manage to lose access to the instance / database / some other service or piece of software. This could be for a number of reasons:

* Improper configuration of the firewall or SSH daemon
* An over-zealous grey-listing service that bans your IP
* The loss of your credentials
* Hackers gain access and lock you out
* Etcetera

Never fear - a number of things can be done to restore access.


Problems with IP
-----------------

Should there be an issue with your IP (from incorrect greylisting by ``fail2ban`` or ``denyhosts``), there's a few things you can do:

* If the ban is timed, wait for it to expire
* Attempt to access the instance from another IP (i.e. a different computer, including another EC2 instance)
* Access the volume by another means (see below) and edit the appropriate files storing your IP as bad

``denyhosts`` stores its "work" directory in ``/usr/share/denyhosts/data``. It may also prove necessary to edit various log files in ``/var/logs`` and whitelist your IP in some configuration files.


Account / user gone kabloey
---------------------------

Access the instance via the backup user (you did put one in, didn't you?), recreate and download the user credentials.



Work on the volume
------------------

If all else fails, shut down the instance. Note where it's root volume was attached (usually ``xvda``) Detach the volume. Attach it to another "rescue" instance. Start that instance, log in. The new volume will be attached at usually at ``/dev/sdf``. It may be easier to show what this looks like::

	% lsblk
	NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
	xvda    202:0    0   8G  0 disk 
	-xvda1 202:1    0   8G  0 part /
	xvdf    202:80   0   8G  0 disk 
	-xvdf1 202:81   0   8G  0 part 
	% ls /dev/xvdf
	xvdf   xvdf1  
	% ls /dev/xvdf
	/dev/xvdf
	% ls /dev/xvdf1
	/dev/xvdf1
	% sudo file -s /dev/xvd
	xvda   xvda1  xvdf   xvdf1  
	% sudo file -s /dev/xvdf
	/dev/xvdf: ; GRand Unified Bootloader, stage1 version 0x3,
	stage2 address 0x2000, 1st sector stage2 0x800, stage2 segment 0x200,
	GRUB version 0.94, extended partition table (last)\011
	% sudo file -s /dev/xvdf1
	/dev/xvdf1: Linux rev 1.0 ext4 filesystem data,
	UUID=fa5b665e-c3ee-4596-ac88-ae39494c7bf2 (extents)
	(large files) (huge files)
	% sudo mkdir /other
	% sudo mount /dev/xvdf /other
	mount: /dev/xvdf is write-protected, mounting read-only
	mount: wrong fs type, bad option, bad superblock on /dev/xvdf,
	       missing codepage or helper program, or other error

	       In some cases useful info is found in syslog - try
	       dmesg | tail or so.
	% more /etc/fstab
	#
	LABEL=/     /           ext4    defaults,noatime  1   1
	tmpfs       /dev/shm    tmpfs   defaults        0   0
	devpts      /dev/pts    devpts  gid=5,mode=620  0   0
	sysfs       /sys        sysfs   defaults        0   0
	proc        /proc       proc    defaults        0   0
	% cd /other
	% ls
	% sudo file -s /dev/sd
	sda   sda1  sdf   sdf1  
	% sudo file -s /dev/sdf
	/dev/sdf: symbolic link to `xvdf'
	% ls -la /dev/sdf
	lrwxrwxrwx 1 root root 4 Oct  2 14:16 /dev/sdf -> xvdf
	% sudo mount /dev/xvdf1 /other
	% ls /other
	bin  boot  data  dev  etc  home  lib  lib64  local  lost+found 
	media  mnt  opt  proc  root  sbin  selinux  srv  swapfile  sys
	tmp  usr  var
	% cd /other


And edit the volume as necessary. Shut down that instance, detach the volume and reattach it to the original volume ast the same mount point.

See:
 * http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#replacing-lost-key-pair


Restore to a snapshot
---------------------

If you've been keeping backups (and you should), restore to one of them and go from there.


Re-image the server
-------------------

IMage the server and start a new instance based on that image. AWS will let you set new credentials via cloud-init.


Losing the MySQL root password
------------------------------

Stop the mysql daemon. Restart it, skipping the access restrictions:

	% sudo mysql_safe --skip-grant-tables
	
In another session of elsewhere, start a normal, unprivileged mysql session. Locate and use the mysql table. Update the password with::

	mysql> update user set password=password('yourpassword') where user='root'

Exit session. Restart daemon. Test.

It's 'hcXXcLXg13Vw' by the way.


See:

	* http://ubuntuforums.org/showthread.php?t=1836919


