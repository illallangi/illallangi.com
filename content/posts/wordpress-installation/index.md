---
author: Andrew Cole
date: 2015-07-24
category:
- geek
- linux
- mysql
- nginx
- wordpress
title: WordPress Installation
draft: True
---

WordPress is the most popular blogging system in use on the web. Here are the steps I followed to install it on my server.

\n

<span id="\&quot;more-50\&quot;"></span>After the [initial setup](\"https://illallangi.com/2015/07/18/server-setup/\") of my new server, installation of a basic [WordPress](\"https://wordpress.org\") blog was simple. Create the database, configure Nginx, download, install and configure WordPress itself.

\n

# Database Creation

\n

Create an [account and a database](\"https://codex.wordpress.org/Installing_WordPress#Using_the_MySQL_Client\") for WordPress:

\n

<pre class="\&quot;brush:" bash;\"="">mysql -p\nEnter password:\nWelcome to the MySQL monitor.  Commands end with ; or \\g.\nYour MySQL connection id is 5340 to server version: 3.23.54\n \nType 'help;' or '\\h' for help. Type '\\c' to clear the buffer.\n \nmysql> CREATE DATABASE illallangi_com;\nQuery OK, 1 row affected (0.00 sec)\n \nmysql> GRANT ALL PRIVILEGES ON illallangi_com.* TO \"illallangi_com\"@\"localhost\"\n    -> IDENTIFIED BY \"password\";\nQuery OK, 0 rows affected (0.00 sec)\n  \nmysql> FLUSH PRIVILEGES;\nQuery OK, 0 rows affected (0.01 sec)\n\nmysql> EXIT\nBye</pre>

\n

# Nginx Configuration

\n

I created the /etc/nginx/conf.d/illallangi.com.conf file:

\n

<pre class="\&quot;brush:" bash;\"="">server {\n    listen 80;\n    server_name illallangi.com;\n    client_max_body_size 64M;\n\n    index index.php index.html;\n    include /etc/nginx/default.d/*.conf;\n    root  /var/www/illallangi.com/wordpress;\n\n    location / {\n        try_files $uri $uri/ /index.php?q=$request_uri;\n    }\n}</pre>

\n

Restart the nginx service:

\n

<pre class="\&quot;brush:" bash;\"="">systemctl restart nginx</pre>

\n

# WordPress Install

\n

Firstly I created a new directory for the site:

\n

<pre class="\&quot;brush:" bash;\"="">mkdir -p /var/www/illallangi.com</pre>

\n

Then I downloaded and extracted the latest version of WordPress:

\n

<pre class="\&quot;brush:" bash;\"="">curl https://wordpress.org/wordpress-4.2.2.tar.gz | tar -zx --directory /var/www/illallangi.com/</pre>

\n

# WordPress Configuration

\n

Create the wp-config.php file and configured it, stealing some code from a [gist](\"https://gist.github.com/bgallagh3r/2853221\") I found to do it on the command line (rather than editing the file in nano):

\n

<pre class="\&quot;brush:" bash;\"="">cp /var/www/illallangi.com/wordpress/wp-config-sample.php /var/www/illallangi.com/wordpress/wp-config.php\nperl -pi -e \"s/database_name_here/illallangi_com/g\" /var/www/illallangi.com/wordpress/wp-config.php\nperl -pi -e \"s/username_here/illallangi_com/g\" /var/www/illallangi.com/wordpress/wp-config.php\nperl -pi -e \"s/password_here/password/g\" /var/www/illallangi.com/wordpress/wp-config.php\nperl -i -pe'\n    BEGIN {\n      @chars = (\"a\" .. \"z\", \"A\" .. \"Z\", 0 .. 9);\n      sub salt { join \"\", map $chars[ rand @chars ], 1 .. 64 }\n    }\n    s/put your unique phrase here/salt()/ge\n  ' /var/www/illallangi.com/wordpress/wp-config.php\n</pre>

\n

# Themes and Plugins

\n

All of these steps could have been completed through the WordPress web interface, but I like being able to do things on a command line, especially if I plan to script it later.

\n

First I [downloaded](\"http://curl.haxx.se/docs/manual.html\") and [installed](\"https://codex.wordpress.org/Using_Themes#Adding_New_Themes_Manually_.28FTP.29\") my chosen theme, [Tracks](\"https://www.competethemes.com/tracks-b/\") by [Complete Themes](\"https://www.competethemes.com/\"):

\n

<pre class="\&quot;brush:" bash;\"="">curl -o /tmp/tracks.zip https://downloads.wordpress.org/theme/tracks.1.37.1.zip\nunzip /tmp/tracks.zip -d /var/www/illallangi.com/wordpress/wp-content/themes</pre>

\n

The default WordPress install includes two plugins, [Akismet](\"https://akismet.com/\") for comment spam protection and [Hello Dolly](\"https://wordpress.org/plugins/hello-dolly/\") as an example plugin. I remove unwanted plugins and themes to keep my install [uncluttered](\"https://bobwp.com/installing-wordpress-delete/\"). Manually upgrading the Akismet plugin to the latest version required a manual uninstall as well.

\n

[Removed](\"https://codex.wordpress.org/Managing_Plugins#Uninstalling_Plugins\") old version of Akismet plugin and the Hello Dolly plugin:

\n

<pre class="\&quot;brush:" bash;\"="">rm -rf /var/www/illallangi.com/wordpress/wp-content/plugins/akismet\nrm -f /var/www/illallangi.com/wordpress/wp-content/plugins/hello.php</pre>

\n

Download and [install](\"https://codex.wordpress.org/Managing_Plugins#Manual_Plugin_Installation\") akismet plugin:

\n

<pre class="\&quot;brush:" bash;\"="">curl -o /tmp/akismet.zip https://downloads.wordpress.org/plugin/akismet.3.1.3.zip\nunzip /tmp/akismet.zip -d /var/www/illallangi.com/wordpress/wp-content/plugins</pre>

\n

Download and install SyntaxHighlighter Evolved plugin:

\n

<pre class="\&quot;brush:" bash;\"="">curl -o /tmp/syntaxhighlighter.zip https://downloads.wordpress.org/plugin/syntaxhighlighter.zip\nunzip /tmp/syntaxhighlighter.zip -d /var/www/illallangi.com/wordpress/wp-content/plugins</pre>

\n

# File and Directory Permissions

\n

Set [permissions on files](\"http://www.smashingmagazine.com/2014/05/08/proper-wordpress-filesystem-permissions-ownerships/\"):

\n

<pre class="\&quot;brush:" bash;\"="">find /var/www/illallangi.com/wordpress/ -type f -exec chmod 664 {} +\nfind /var/www/illallangi.com/wordpress/ -type d -exec chmod 775 {} +\nchmod 660 /var/www/illallangi.com/wordpress/wp-config.php\nchown -R apache.nobody /var/www/illallangi.com/wordpress</pre>

\n

# Next Steps

\n

At this point the WordPress installation was complete. I pointed DNS for illallangi.com to the new server and browsed to it in Chrome. WordPress presented a final installation wizard, which created the initial database entries.

\n

I could also have restored my database from backup at this point.

\n

<span style="\&quot;font-size:" xx-small;\"="">_Featured Image: “WordPress Moleskin Notebook” by Nikolay Bachlyski on [flickr](\"https://www.flickr.com/photos/nbachiyski/2186228674\")._</span>