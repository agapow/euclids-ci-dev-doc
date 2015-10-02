Hosting multiple & seperate sites on same server
================================================


Assumptions: using Apache2 on


Location and configuration

The ServerRoot directory is where everything happens and is usually /etc/apache2

The main configuration file is apache2.conf. There's some argument for not fiddling with this. httpd.conf is also provided and is actually empty. 


Site configurations appear in sites-available, that are enabled by symlinking into sites-enabled. These are just configuration files that are parsed like the others.


There are various bits of advice about not modifying your core httpd.conf file, in case it gets wiped out by an upgrade or other activity. It also keeps your modifications obvious. In that case, use an include directive


     Include custom/*.conf


You can include a directory, and thus get everything in it, but this may lead you to include temporary or unintentional files.


Here I've included two files:


   * security: various security settings for blocking access to various places and providing global error files.
   * sites: global types and module activation

Whenever you change a configuration or enable or disable a site as below, be sure to reload apache:


     % /etc/init.d/apache2 reload


The actual site data is stored in /var/www


Various environmental variables are stored in ServerRoot/envvars



Creating and activating sites


Sites can be enabled with a2ensite which symlinks the site file into the sites-enabled dircectory They are disabled with with a2dissite. Yes, this makes no sense.


% a2ensite <site>
% a2dissite <site>


Using either without an argument lists the possible sites they can be used on


You can create a new site by creating the necessary config file in site-available and then enabling it.  For example, here the zope site configuration:

<VirtualHost *:80>
   ServerName agapow.net
   ServerAlias www.agapow.net
   RewriteEngine On
   #RewriteCond  %{SERVER_NAME}  ^(www\.)?agapow\.net$
   RewriteRule ^($|/.*) \http://127.0.0.1:8080/VirtualHostBase/\http/%{SERVER_NAME}:80/agapow/VirtualHostRoot$1 [L,P]


<Proxy *>
Order Deny,Allow
Allow from all
</Proxy>


</VirtualHost>
There are default sites provided, that may have their name prefixed by 000, so they are loaded first. May have to disable these to get your custom sites to show up.








The handy script for making compartmentalised sites is called a2mksite. Note that this calls sudo itself and is aliased in your ~/.bashrc file.




Setting up a cgi site


In this case, we are setting up cgi on a subdomain cgi.agapow.net


<VirtualHost *:80>


     # so the requests are sent here correctly
        ServerName cgi.agapow.net

        DocumentRoot /var/www/sites/cgi.agapow.net/public

        UseCanonicalName Off

        CustomLog /var/www/sites/cgi.agapow.net/log/access.log combined
        ErrorLog /var/www/sites/cgi.agapow.net/log/error.log
        LogLevel warn

        <IfModule mod_ssl.c>
                SSLEngine off
        </IfModule>


     # anything going to urls /cgi-bin/foo will be treated according
        ScriptAlias /cgi-bin/ /var/www/sites/cgi.agapow.net/public/cgi-bin/
        <Directory "/var/www/sites/cgi.agapow.net/public/cgi-bin/">
                AllowOverride None
                Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
                AddHandler cgi-script cgi pl py
                Order allow,deny
                Allow from all
        </Directory>

</VirtualHost>



also chmod a+x the executable 755?


if it has a shebang line, make sure it points in the right directuion


if you get a "prematurte end of script headers script", this means that the script has died before generating headers


call the script on the commandline to see it actually work


execute permission is required on files to read and write

You can staically serve files from the dit if you explciuitly set a handler:
AddHandler default-handler .html .htm .gif .jpg

TODO: unclear how to set root directory as cgi - does it require ScriptAlias

