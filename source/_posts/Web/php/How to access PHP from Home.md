---
title: php_from_home
categories:
  - Web
  - php
tags: 'Website, Host, localhost, home, apache, php, host, directory,'
date: 2018-11-01 22:09:34
---

This is a documentation to enable apache2 in home directory. Feel free to try out this hack, if you are too tired of copying files to `/var/www/html/` folder.
<!--more-->
# Apach2 at Home Directory

## Enable userdir module
```
sudo a2enmod userdir
```
This module allows user-specific directories to be accessed using the http://example.com/~user/ syntax. 

## Allow PHP to be executed in user directory

#### Commenting php-mods config
You we have to configure php-mods config file.  The config file varies based on your PHP version. 
Execute the following command (for php5)
```
sudo vim /etc/apache2/mods-available/php5.conf
```

Or this if you use php7.0
```
sudo vim /etc/apache2/mods-available/php7.0.conf
```

You can even use `gedit` or any text editor of your choice if you are not familiar with vim commands.
Next Comment the following part in the file.

```
#<IfModule mod_userdir.c>
#    <Directory /home/*/public_html>
#        php_admin_flag engine Off
#    </Directory>
#</IfModule>

```

#### Enabling Directory Listing
To enable Directory Listing for apache server, add the following command to apache2.config file 
>sudo vim /etc/apache2/apache2.conf

At the very bottom of the file, add this command
```
<Directory /home/*/public_html/>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>
```

## Restart Apache
Restart the apache to apply the new configurations.
>sudo service apache2 restart


## Enjoy!!
Thats the end! Now you can enjoy Apache server your home directory. To start with, copy the php files to **/home/<username>/public_html** and then visit
`https://localhost/~username`
from the web browser.

### Reference
[askubuntu](https://askubuntu.com/a/518188)
