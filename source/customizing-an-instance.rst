Customizing a server from a base instance
=========================================

For convenience, a base image has been created that should have most of the bells and whistles pre-confiugured. Future servers shiould be base dupon this, to save work and ensure consistency and security


Preconfiguration
----------------

This is an incomplete list of the things the base server has had done to it:

* fail2ban and denyhosts installed
* SSH port shifted to 811
* Mailserver set up and pointed at SES service
* Swap file setup



Changes
-------

* Delete all the Redmine & PHPMyAdmin stuff if possible
* Shut down Apache if need be
* Change the system nickname
