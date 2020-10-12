---
author: Andrew Cole
date: 2015-09-27
category:
- geek
title: OpenFlights Installation
draft: True
---

OpenFlights is an open-source tool that lets you map your flights around the world. Here are the steps I followed to install it on my server.

\n

<span id="\&quot;more-130\&quot;"></span>_**Update 2015-09-27:** Updated nginx configuration to allow extensionless php files._

\n

After the [initial setup](\"https://illallangi.com/2015/07/18/server-setup/\") of my new server, installation of [OpenFlights](\"http://openflights.org/\") was simple. Create the database, configure Nginx, download, install and configure WordPress itself.

\n

# OpenFlights Install

\n

Firstly I created a new directory for the site:

\n

<pre class="\&quot;brush:" bash;\"="">mkdir -p /var/www/flights.illallangi.com</pre>

\n

Then I downloaded and extracted the latest version of OpenFlights:

\n

<pre class="\&quot;brush:" bash;\"="">curl -L -o /tmp/openflights.zip [https://github.com/jpatokal/openflights/archive/master.zip](\"https://github.com/jpatokal/openflights/archive/master.zip\" "\"https://github.com/jpatokal/openflights/archive/master.zip\"")</pre>

\n

<pre class="\&quot;brush:" bash;\"="">unzip /tmp/openflights.zip -d /var/www/flights.illallangi.com</pre>

\n

<pre class="\&quot;brush:" bash;\"="">mv /var/www/flights.illallangi.com/openflights-master /var/www/flights.illallangi.com/openflights</pre>

\n

# Database Creation

\n

Create an account and a database for OpenFlights:

\n

<pre class="\&quot;brush:" bash;\"="">mysql -p\nEnter password:\nWelcome to the MySQL monitor.  Commands end with ; or \\g.\nYour MySQL connection id is 5340 to server version: 3.23.54\n \nType 'help;' or '\\h' for help. Type '\\c' to clear the buffer.\n \nmysql> CREATE DATABASE flights;\nQuery OK, 1 row affected (0.00 sec)\n \nmysql> GRANT ALL PRIVILEGES ON flights.* TO [\"flights\"@\"localhost\"\n](\"mailto:"flights\\"@"localhost"\")    -> IDENTIFIED BY \"password\";\nQuery OK, 0 rows affected (0.00 sec)\n  \nmysql> FLUSH PRIVILEGES;\nQuery OK, 0 rows affected (0.01 sec)\n\nmysql> EXIT\nBye</pre>

\n

Create OpenFlights tables:

\n

<pre class="\&quot;brush:" bash;\"="">cat /var/www/flights.illallangi.com/openflights/sql/create.sql | grep -v CREATE\\ [UD] | grep -v GRANT | grep -v CONNECT | mysql -D flights -p</pre>

\n

Import initial data:

\n

<pre class="\&quot;brush:" bash;\"="">perl -pi -e \"s/data\\//\\/var\\/www\\/flights.illallangi.com\\/openflights\\/data\\//g\" /var/www/flights.illallangi.com/openflights/sql/load-data.sql\nperl -pi -e \"s/locale\\//\\/var\\/www\\/flights.illallangi.com\\/openflights\\/locale\\//g\" /var/www/flights.illallangi.com/openflights/sql/load-data.sql\ncat /var/www/flights.illallangi.com/openflights/sql/load-data.sql | mysql -D flights -p --local-infile</pre>

\n

# Nginx Configuration

\n

I created the /etc/nginx/conf.d/flights.illallangi.com.conf file:

\n

<pre class="\&quot;brush:" bash;\"="">server {\n listen 80;\n server_name flights.illallangi.com;\n\n index index.php index.html;\n include /etc/nginx/default.d/*.conf;\n root /var/www/flights.illallangi.com/openflights;\n\n location / {\n try_files $uri $uri/ @php;\n }\n\n location @php {\n fastcgi_param SCRIPT_FILENAME \"$document_root$uri.php\";\n fastcgi_param PATH_TRANSLATED \"$document_root$uri.php\";\n fastcgi_param QUERY_STRING $args;\n\n include fastcgi_params;\n fastcgi_pass 127.0.0.1:9000;\n fastcgi_index index.php;\n }\n}\n\n</pre>

\n

Restart the nginx service:

\n

<pre class="\&quot;brush:" bash;\"="">systemctl restart nginx</pre>

\n

# OpenFlightsConfiguration

\n

Create the wp-config.php file and configured it, stealing some code from a [gist](\"https://gist.github.com/bgallagh3r/2853221\") I found to do it on the command line (rather than editing the file in nano):

\n

<pre class="\&quot;brush:" bash;\"="">cp /var/www/flights.illallangi.com/openflights/php/config.php.sample /var/www/flights.illallangi.com/openflights/php/config.php\nperl -pi -e \"s/flightdb2/flights/g\" /var/www/flights.illallangi.com/openflights/php/config.php\nperl -pi -e \"s/openflights/flights/g\" /var/www/flights.illallangi.com/openflights/php/config.php\nperl -pi -e \"s/\\\"\\\"/\\\"password\\\"/g\" /var/www/flights.illallangi.com/openflights/php/config.php\n</pre>

\n\n

# File and Directory Permissions

\n

Set [permissions on files](\"http://www.smashingmagazine.com/2014/05/08/proper-wordpress-filesystem-permissions-ownerships/\"):

\n

<pre class="\&quot;brush:" bash;\"="">find /var/www/flights.illallangi.com/openflights -type f -exec chmod 664 {} +\nfind /var/www/flights.illallangi.com/openflights -type d -exec chmod 775 {} +\nchown -R apache.nobody /var/www/flights.illallangi.com/openflights</pre>

\n

# Next Steps

\n

At this point the OpenFlights installation was complete. I pointed DNS for flights.illallangi.com to the new server and browsed to it in Chrome. OpenFlights displayed its initial login.

\n

I could also have restored my database from backup at this point.

\n

<span style="\&quot;font-size:" xx-small;\"="">_Featured Image: “Contrail Indicated” by Zach Stern on [flickr](\"https://www.flickr.com/photos/zachstern/14370526025/\")._</span>