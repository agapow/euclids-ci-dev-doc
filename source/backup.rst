Backup
======

There are a number of possible backup tools or approaches. Having triued and settle dupon one, the notes for the others is kept here as a historical record.


ec2-automate-backup
-------------------

The "AWS mssing tools" package provides a series of scripts for assorted administrative tasks, including the "ec2-autmate-backup" scripts. Like it initially seemd fine, some problems result:

* It's underdocumented.

* There are two versions, one which is a pure shell script and the other which uses the awscli tools. The interface of the two differs slightly and the use of the second is strongly recommended.

* What is not made clear is that some described behaviour has to be triggered by settings to be set inside the script or defined as environmental variables:

	* ``name_tag_create=true`` is required to name the created snapshot
	* ``purge_snapshots=false`` is required for old snapshots to be purged
	
* The region must be explicitly provided with the ``-r`` option

* There's some exotic syntax that requires you pass an argument saying how the volumes will be selected then pass another argument doing the exact selection.

* Some examples::

	# in the EU-West region, backup the volumes with a 'DailyBackup' tag
	# set to 'true', give them a lifetime of 14 days and purge backups
	# older than this
	% ec2-automate-backup-awscli.sh -r eu-west-1 -s tag -t \
		"DailyBackup,Values=true" -k 14 -p
	
* Running different backup sets in tricky when it comes to purging. The purge works over all snapshots that have an expire tag

* The naming of the snapshots is a little cryptic - essentially it gives them the name of the backup volume plus the date ... in epoch (seconds) mode. This can be sorted by editting the script.

* There were some subtle issues in using this. For unclear reasons, some volumes would not be selected, or selection varied when called by cron. Thus, the decision was made to look for other solutions.

See:

* https://github.com/colinbjohnson/aws-missing-tools/tree/master/ec2-automate-backup


Roll you own with boto
----------------------

The boto python package provides a scfripting interface to AWS services and it is trivial to make a backup script. See ``backupset.py``.


makesnapshots
-------------

This was the solution settled upon - it's simple, robust and "just works". It uses boto and, if boto is set up correctly, just reads your AWS credentials from those stored locally in a config file.

Relevant settings are::

	# number of snapshost to keepo of each
	'keep_day': 4,
	'keep_week': 4,
	'keep_month': 3,

	# Tag of the EBS volume you want to take the snapshots of
	'tag_name': 'Backup',
	'tag_value': 'true',

	# ARN of the SNS topic (optional)
	'arn': 'arn:aws:sns:eu-west-1:481187277202:euclids-backup',

	# EC2 info about your server's region
	'ec2_region_name': 'eu-west-1',
	'ec2_region_endpoint': 'ec2.eu-west-1.amazonaws.com',	 

The following crontab is used to trigger it::

	30 1 * * 1-5 makesnapshots.py day
	30 2 * * 6 makesnapshots.py week
	30 3 1 * * makesnapshots.py month

..note::
	The path may have to be given in full. Also note that if the Mac is not on or is sleeping, it will not run. However, it was felt for the meanwhile to be better to trigger a backup from offsite rather than from one of the machines being backed up.
	
See: 

* https://github.com/evannuil/aws-snapshot-tool
* http://www.coresoftwaregroup.com/blog/automated-amazon-ebs-volume-snapshots-with-boto

# TODO: use more restrictive backup user
# TODO: run off one of the servers
