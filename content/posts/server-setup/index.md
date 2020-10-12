---
author: Andrew Cole
date: 2015-07-18
category:
- geek
- linux
- mysql
- nginx
- php
title: Server Setup
draft: True
---

So as I'm giving this blogging thing another try, I thought I'd detail the steps I followed to setup the new server.

\n

<span id="\&quot;more-15\&quot;"></span>

\n

_**Update 2015-07-20:** Added post_max_size to Configure PHP-FPM, php-xml to Update and Install Packages, and added Configure Mail_

\n

I've been a happy Linode customer for a long time, running a couple of VMs there for the various web pages I support (those not hosted on GitHub Pages). As my current Linode was running a now old version of CentOS (6.6), I thought I'd stand up a new server with CentOS 7 on it, get the blog up and running, and then migrate my other sites over. This also gives me a chance to use Linodes new, and closer to Australia, Singapore datacenter (my old server is hosted in Newark). This also gave me a chance to test the backup and restoration of WordPress, as I'll blew away and recreated the server until I was comfortable with it.

\n

# Overview

\n

The server runs the following components:

\n

*   [Nginx](\"http://nginx.org/\"): a HTTP server and reverse proxy server, replacing the more traditional Apache web server;

*   [PHP-FPM](\"http://php-fpm.org/\"): a FastCGI Process Manager, enable the use of PHP in Nginx; and

*   [MariaDB](\"https://mariadb.org/\"): a drop-in replacement for MySQL, for SQL databases.

\n

This is the first time I have used the LEMP (Linux, Nginx, MariaDB, PHP) stack on a server, replacing the traditional LAMP (Linux, Apache, PHP and MySQL) stack. I decided to move to LEMP as I have been using Nginx more and more for its forward proxy abilities, and it didn't make sense to install heavyweight Apache just for its PHP support.

\n

# Create Server

\n

I created a server following the [Getting Started](\"https://www.linode.com/docs/getting-started\") instructions at Linode. I picked a **Linode 1024** server as it's the cheapest server and its specs will be more than enough for the few websites it'll be running.

\n

Speaking of specs:

\n

*   Hard Drive: 24GB

*   CPU: 1 CPU Core

*   RAM: 1GB RAM

*   Traffic: 2TB Traffic/Month

\n

All this for a low price of USD10/Month. New customers can get their first month free through an offer from [Treehouse](\"https://teamtreehouse.com/join/free-month?utm_source=linode&utm_medium=partnership&utm_campaign=linode-2013&cid=1124\").

\n

I set the hostname following the Getting Started instructions as well:

\n

<pre class="\&quot;brush:" bash;\"="">hostnamectl set-hostname hostname</pre>

\n

# Update and Install Packages

\n

As Nginx is not in the default CentOS 7 package repositories, first I enabled the [Extra Packages for Enterprise Linux](\"https://fedoraproject.org/wiki/EPEL\") (EPEL) repository:

\n

<pre class="\&quot;brush:" bash;\"="">yum install -y epel-release</pre>

\n

Then I installed the required packages (including nano as its my preferred text editor):

\n

<pre class="\&quot;brush:" bash;\"="">yum install -y mariadb-server nginx\nyum install -y php php-fpm php-gd php-mysql php-mbstring php-xml\nyum install -y zip unzip bzip2 nano\nyum install -y postfix</pre>

\n

Then I updated all installed packages:

\n

<pre class="\&quot;brush:" bash;\"="">yum update -y</pre>

\n

I then rebooted to allow all updates to take effect:

\n

<pre class="\&quot;brush:" bash;\"="">reboot</pre>

\n

# Configure Firewall

\n

I enabled and started the Firewalld service:

\n

<pre class="\&quot;brush:" bash;\"="">systemctl enable firewalld\nsystemctl start firewalld</pre>

\n

Note that by default ssh is allowed through the firewall, or else these commands would have locked me out of my server. Fortunately, if that happened I could have accessed the console for my virtual machine through the [Linode Shell](\"https://www.linode.com/docs/networking/using-the-linode-shell-lish\") (lish)

\n

# Configure Mail

\n

While the server will not be being used as a mail server, applications will be trying to send mail out, hence a local mail system is required.

\n

I created the /root/.forward file:

\n

<pre class="\&quot;brush:" bash;\"="">andrew.cole@illallangi.com</pre>

\n

And I enabled and started the PostFix service:

\n

<pre class="\&quot;brush:" bash;\"="">systemctl enable postfix\nsystemctl start postfix</pre>

\n

# Configure PHP-FPM

\n

First I updated the php.ini file to allow larger POSTs:

\n

<pre class="\&quot;brush:" bash;\"="">perl -pi -e \"s/post_max_size = .*M/post_max_size = 64M/g\" /etc/php.ini</pre>

\n

Then I enabled and started the PHP-FPM service:

\n

<pre class="\&quot;brush:" bash;\"="">systemctl enable php-fpm\nsystemctl start php-fpm</pre>

\n

# Configure MariaDB

\n

I enabled and started the MariaDB service:

\n

<pre class="\&quot;brush:" bash;\"="">systemctl enable mariadb\nsystemctl start mariadb</pre>

\n

Then I secured the MariaDB installation with the provided script:

\n

<pre class="\&quot;brush:" bash;\"="">mysql_secure_installation</pre>

\n\n

I answered all the prompts, which set the root password, removed unauthenticated access, and removed the default test databases.

\n

# Configure Nginx

\n

First to enable PHP-FPM support in Nginx I created the /etc/nginx/default.d/php-fpm.conf file:

\n

<pre class="\&quot;brush:" bash;\"="">location ~ [^/]\\.php(/|$) {\n    fastcgi_split_path_info ^(.+?\\.php)(/.*)$;\n    include fastcgi_params;\n    fastcgi_pass 127.0.0.1:9000;\n    fastcgi_index index.php;\n    fastcgi_param SCRIPT_FILENAME $document_root/$fastcgi_script_name;\n}</pre>

\n

All files in the default.d directory are included in the default site config (through a line in nginx.conf). These files will also be included in my custom server files (which will be stored in the conf.d directory).

\n

I enabled and started the Nginx service:

\n

<pre class="\&quot;brush:" bash;\"="">systemctl enable nginx\nsystemctl start nginx</pre>

\n

By default port 80 is blocked by the firewall, so I added an exception for inbound HTTP traffic:

\n

<pre class="\&quot;brush:" bash;\"="">firewall-cmd --permanent --zone=public --add-service=http\nfirewall-cmd --reload</pre>

\n

# Testing

\n

By entering the IP address of my new server into my browser of choice (currently Chrome), I confirmed Nginx was functioning as expected when I was greeted by the Nginx Fedora test page:

\n

[![\"screencapture-192-168-198-128-1437217369742\"](\"https://i0.wp.com/illallangi.com/wp-content/uploads/2015/07/screencapture-192-168-198-128-1437217369742_thumb.png?resize=244%2C140\" "\"screencapture-192-168-198-128-1437217369742\"")](\"https://i0.wp.com/illallangi.com/wp-content/uploads/2015/07/screencapture-192-168-198-128-1437217369742.png\")

\n

To ensure the PHP-FPM support was working, I created a simple php file at /usr/share/nginx/html/info.php:

\n

<pre class="\&quot;brush:" bash\"=""><?php phpinfo(); ?></pre>

\n

Entering the path to this file in my browser I was greeted by the (ridiculously long) phpinfo() output:

\n

[![\"screencapture-192-168-198-128-info-php-1437217406152\"](\"https://i1.wp.com/illallangi.com/wp-content/uploads/2015/07/screencapture-192-168-198-128-info-php-1437217406152_thumb.png?resize=244%2C140\" "\"screencapture-192-168-198-128-info-php-1437217406152\"")](\"https://i1.wp.com/illallangi.com/wp-content/uploads/2015/07/screencapture-192-168-198-128-info-php-1437217406152.png\")

\n

# Next Steps

\n

This completed the initial setup of the server with a functional LEMP stack. Next up was to install WordPress, which will be the subject of a future post.

\n

<font size="\&quot;1\&quot;">_Featured Image: \u201cheat shrinkage\u201d by eyemage on [flickr](\"https://www.flickr.com/photos/eyemage/2553068378\")._</font>