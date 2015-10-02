Creating an EC2 instance
========================

De novo
-------

#. Login into aws.amazon.com

   .. note:
      If you have logged into other console (e.g. a private one), it may
      have left a cookie directing you there. Use the link to go to general
      login or vice versa.
      
   .. note::
      If you've set this up, it may require an authentication device. The
      ID is "mfa" for some reason.
      
#. Go to the console

#. Pick the region you wish

#. Create an instance

   .. note::
      Lots could be done here. The bare minimum is to issue a pem key for
      SSHing in and setting security rules allowing for SSH. Save this file - it is irreplacable.
      
   .. note::
      The differences between the various images can be hard to discern. 
      
#. Launch the instance
         
#. Check your pem key. It should be readable only by you. chmod 400 if need be. If permissions are too liberal, the instance will log you out. You may have to chown the file.

#. SSH into the instance. The username will depend on the image you launched. For amazon it is "ec2-user", although it is alleged that root works. See `http://alestic.com/2014/01/ec2-ssh-username`::

   % ssh -i <key>.pem <user>
   % ssh -i agapow-redcap-demo.pem ec2-user@ec2-54-76-2-49.eu-west-1.compute.amazonaws.com
   
#. The instance may immediately demand a software update. Do it.


From AMI
--------

foo

