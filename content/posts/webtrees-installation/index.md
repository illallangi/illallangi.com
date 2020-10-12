---
author: Andrew Cole
date: 2015-07-31
category:
- geek
- linux
- mysql
- webtrees
title: Webtrees Installation
draft: True
---

Webtrees is the web’s leading on-line collaborative genealogy application. Here are the steps I followed to install it on my server.

\n

<span id="\&quot;more-81\&quot;"></span>After the [initial setup](\"https://illallangi.com/2015/07/18/server-setup/\") of my new server, installation of a basic [Webtrees](\"https://www.webtrees.net\") site was simple. Create the database, configure Nginx, download, install and configure Webtrees itself.

\n

# Database Creation

\n

Create an [account and a database](\"https://codex.wordpress.org/Installing_WordPress#Using_the_MySQL_Client\") for Webtrees:

\n

<pre class="\&quot;brush:" bash;\"="">mysql -p\nEnter password:\nWelcome to the MySQL monitor.  Commands end with ; or \\g.\nYour MySQL connection id is 5340 to server version: 3.23.54\n \nType 'help;' or '\\h' for help. Type '\\c' to clear the buffer.\n \nmysql> CREATE DATABASE alltheforest_com;\nQuery OK, 1 row affected (0.00 sec)\n \nmysql> GRANT ALL PRIVILEGES ON alltheforest_com.* TO \"alltheforest_com\"@\"localhost\"\n    -> IDENTIFIED BY \"password\";\nQuery OK, 0 rows affected (0.00 sec)\n  \nmysql> FLUSH PRIVILEGES;\nQuery OK, 0 rows affected (0.01 sec)\n\nmysql> EXIT\nBye</pre>

\n

# Nginx Configuration

\n

I created the /etc/nginx/conf.d/alltheforest.com.conf file:

\n

<pre class="\&quot;brush:" bash;\"="">server {\n    listen 80;\n    server_name alltheforest.com;\n    client_max_body_size 64M;\n\n    index index.php index.html;\n    include /etc/nginx/default.d/*.conf;\n    root  /var/www/alltheforest.com/webtrees;\n\n    location / {\n        try_files $uri $uri/ /index.php?q=$request_uri;\n    }\n}</pre>

\n

Restart the nginx service:

\n

<pre class="\&quot;brush:" bash;\"="">systemctl restart nginx</pre>

\n

# Webtrees Install

\n

Firstly I created a new directory for the site:

\n

<pre class="\&quot;brush:" bash;\"="">mkdir -p /var/www/alltheforest.com</pre>

\n

Then I downloaded and extracted the latest version of Webtrees:

\n

<pre class="\&quot;brush:" bash;\"="">curl -L https://launchpad.net/webtrees/1.6/1.6.2/+download/webtrees-1.6.2.zip -o /tmp/webtrees.zip\nunzip /tmp/webtrees.zip -d /var/www/alltheforest.com</pre>

\n

Using Curl to download webtrees I had to include the -L parameter to tell Curl to [follow redirects](\"http://curl.haxx.se/docs/faq.html#How_do_I_tell_curl_to_follow_HTT\").

\n

# Themes and Plugins

\n

First I downloaded and installed my chosen [theme](\"https://www.webtrees.net/index.php/en/add-ons/category/4-themes\"), Rural by [Jaubart](\"http://genea.jaubart.com/\"):

\n

<pre class="\&quot;brush:" bash;\"="">curl -o /tmp/rural.zip http://www.jaubart.com/storage/rural-webtrees-1_6_2.zip\nunzip /tmp/rural.zip -d  /var/www/alltheforest.com/webtrees/themes</pre>

\n

# File and Directory Permissions

\n

Set permissions on files, and [change the context](\"https://wiki.centos.org/HowTos/SELinux#head-0f6390ddacfab39ee973ed8018a32212c2a02199\") on the data directory so nginx can write to it:

\n

<pre class="\&quot;brush:" bash;\"="">find /var/www/alltheforest.com/webtrees/ -type f -exec chmod 664 {} +\nfind /var/www/alltheforest.com/webtrees/ -type d -exec chmod 775 {} +\nchown -R apache.nobody /var/www/alltheforest.com/webtrees\nchcon -hR system_u:object_r:httpd_sys_content_t /var/www/alltheforest.com/webtrees/data/</pre>

\n

Note that if the chcon line results in an error about a missing context, remove the selinux-policy package and re-run the command:

\n

<pre class="\&quot;brush:" bash;\"="">yum erase selinux-policy\nchcon -hR system_u:object_r:httpd_sys_content_t /var/www/alltheforest.com/webtrees/data/</pre>

\n

# Next Steps

\n

At this point the Webtrees installation was complete. I pointed DNS for alltheforest.com to the new server and browsed to it in Chrome. WebTrees presented a final installation wizard, which created the initial database entries.

\n

I could also have restored my database from backup at this point, however would need to ensure the restored /var/www/alltheforest.com/webtrees/data/config.ini.php file had the correct database details.

\n

<span style="\&quot;font-size:" xx-small;\"="">_Featured Image: “An outdoor zither lesson” by pellethepoet on [flickr](\"https://www.flickr.com/photos/pellethepoet/7099036227\")._</span>