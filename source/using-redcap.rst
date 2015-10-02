Using REDCap
============

Administration tasks
--------------------

Resetting a users password
~~~~~~~~~~~~~~~~~~~~~~~~~~

Should you need to reset and send a login prompt, browse or search the user table, click on the desired user details, and then click on the reset button. Note: there is no way to do this for a whole group of users - it has to be done one at a time.


Other notes
-----------

* Access to REDCap and setting up accounts absolutely requires the use of a working mail agent.

* There seems to be an odd problem where you try to use more than one acount through on the same browser / computer, where you log out of one account but aren't truly out of it when you try to log into the other. 

* Within a project, it seems like a user can only have one set of permissions, e.g. a single user cannot be in the groups "data entry" and "manager" at the same time.


Importing data
--------------

The following process is used to import monthly Inform datadumps into REDCap:

Run *build_db_from_datadump* on the directory containing the datadump and it will create an SQLite database of the contents:

	usage: build_db_from_datadump.py [-h] [--outdb OUTDB] indir

	positional arguments:
	  indir          directory of csv files

	optional arguments:
	  -h, --help     show this help message and exit
	  --outdb OUTDB  path to create databse on

Next, run *extract_upload_from_db* on that database and a file containg a (row-based) import template for the REDCap database. It will produce a CSV file to be imported into REDCap. It will warn you of:

* fields (column headers) that are in the import template but not the produced CSV
* conversely, fields that are in the CSV but not the import template

General usage is::

	usage: extract_upload_from_db.py [-h] [-i INDB] [-o OUTCSV]
	                                 [-t IMPORT_TEMPLATE] [-d DATA_DICT]

	optional arguments:
	  -h, --help            show this help message and exit
	  -i INDB, --indb INDB  database for reading
	  -o OUTCSV, --outcsv OUTCSV
	                        csv file to be created
	  -t IMPORT_TEMPLATE, --import-template IMPORT_TEMPLATE
	                        import template for usinform
	  -d DATA_DICT, --data-dict DATA_DICT
	                        data dictionary for usinform
