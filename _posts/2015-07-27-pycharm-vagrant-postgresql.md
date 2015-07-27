---
layout: post
title:  "How to connect PyCharm to postgresql using Vagrant"
date:   2015-07-27 15:15:00
comments: true
tags: pycharm vagrant postgresql
---
If you want to connect PyCharm to postgresql data source, it is pretty easy to do.

Prerequisites:

 - PyCharm (I think this will also work in other JetBrains products)
 - Vagrant box with Postgresql inside it. I assume Vagrant is already configured with PyCharm
 - Postgresql [jdbc driver](https://www.jetbrains.com/pycharm/help/configuring-a-db-data-source.html) installed

### Step 1
Click on a green plus button in a "Database" editor tab. Then choose "PostgreSQL" and enter your settings. If you didn't change PostgreSQL settings, defaults should be ok.

<a  href="#" data-featherlight="/images/2015-07/psql_connection.jpg">![PostgreSQL connection settings](/images/2015-07/psql_connection.jpg)</a>

Don't forget to enter your database name, username and password. Host should be `localhost` or `127.0.0.1`, because we are going to connect through SSH tunnel.

### Step 2
Go to "SSH/SSL" tab and check "Use SSH tunnel" checkbox. Then set proxy host to `127.0.0.1`, port to `2222`, proxy user to `vagrant` and password to `vagrant`.

<a  href="#" data-featherlight="/images/2015-07/psql_ssh.jpg">![PostgreSQL SSH settings](/images/2015-07/psql_ssh.jpg)</a>

> I tried connecting with Vagrant private key (which is usually located in user home folder under `/home/user/vagrant.d/insecure_private_key`), but that didn't work: I only saw "Auth fail" message

### Step 3
Now you are ready to press "Test connection" button on a "Database" tab. You should see a message telling that connection is successfull:

<a  href="#" data-featherlight="/images/2015-07/psql_ok.jpg">![PostgreSQL SSH success](/images/2015-07/psql_ok.jpg)</a>
