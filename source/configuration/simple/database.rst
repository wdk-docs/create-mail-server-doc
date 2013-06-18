数据库
=========

MySQL
-----------------

安装
^^^^^^^^^^^^^^^^^

sudo aptitude install mysql-client mysql-server

This will prompt you for a root password. Choose someting wise and remember it! For purpose of this tutorial I will set it to rootPASSWORD

配置
^^^^^^^^^^^^^^^^^

Now we will need to create the tables for thos lookups just specified. First you need to create a user to use in MySQL for mail only. Then you need to create the database, Take note of your chosen mail username and password. You will need the password you specified for root during MySQL package installation.

.. code-block:: sh

   # If not already done (in package installation)...
   mysqladmin -u root -p password new_password
   # log in as root
   mysql -u root -p
   # then enter password for the root account when prompted

Enter password:

.. code-block:: mysql

   # then we create the mail database
   create database maildb;
   # then we create a new user: "mail"
   GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP
   ON maildb.* TO 'mail'@'localhost' IDENTIFIED by 'mailPASSWORD';
   GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP
   ON maildb.* TO 'mail'@'%' IDENTIFIED by 'mailPASSWORD';
   exit;

Obviously replace mailPASSWORD with your chosen password!

Then you will need to create these tables:

* aliases
* domains
* users

We will create more later on for further extensions, but only these are relevant now.

Log in to mysql as the new mail user

.. code-block:: sh

   mysql -u mail -p maildb
   # enter the newly created password

Enter password:

Then run this commands to create the tables:

.. code-block:: mysql

   CREATE TABLE `aliases` (
      `pkid` smallint(3) NOT NULL auto_increment,
      `mail` varchar(120) NOT NULL default '',
      `destination` varchar(120) NOT NULL default '',
      `enabled` tinyint(1) NOT NULL default '1',
      PRIMARY KEY  (`pkid`),
      UNIQUE KEY `mail` (`mail`)
   ) ;
   
   CREATE TABLE `domains` (
      `pkid` smallint(6) NOT NULL auto_increment,
      `domain` varchar(120) NOT NULL default '',
      `transport` varchar(120) NOT NULL default 'virtual:',
      `enabled` tinyint(1) NOT NULL default '1',
      PRIMARY KEY  (`pkid`)
   ) ;
   
   CREATE TABLE `users` (
      `id` varchar(128) NOT NULL default '',
      `name` varchar(128) NOT NULL default '',
      `uid` smallint(5) unsigned NOT NULL default '5000',
      `gid` smallint(5) unsigned NOT NULL default '5000',
      `home` varchar(255) NOT NULL default '/var/spool/mail/virtual',
      `maildir` varchar(255) NOT NULL default 'blah/',
      `enabled` tinyint(3) unsigned NOT NULL default '1',
      `change_password` tinyint(3) unsigned NOT NULL default '1',
      `clear` varchar(128) NOT NULL default 'ChangeMe',
      `crypt` varchar(128) NOT NULL default 'sdtrusfX0Jj66',
      `quota` varchar(255) NOT NULL default '',
      `procmailrc` varchar(128) NOT NULL default '',
      `spamassassinrc` varchar(128) NOT NULL default '',
      PRIMARY KEY  (`id`),
      UNIQUE KEY `id` (`id`)
   ) ;

The last few fields in the users table are not required, but useful if you extend later.

.. code-block:: mysql

   # To visualise the tables created:
   describe aliases;
   describe domains;
   describe users; 
   # then quit mysql
   exit;

Next is to edit the MySQL's my.cnf file. In Ubuntu/debian this is created by default. In Mandrake I had to manually create a blank one in /etc. But we need to configure it, so:

.. code-block:: sh

   sudo vi /etc/mysql/my.cnf

In previous version you needed to comment out this line

.. code-block:: ini

   #skip-networking

However in todays file the default is to bind the address to localhost, which is fine.

.. code-block:: ini

   bind-address = 127.0.0.1

It is very useful at the start to log any SQL calls that makes it to MySQL. So enable these lines:

.. code-block:: ini

   general_log_file = /var/log/mysql/mysql.log
   general_log = 1

Then in a few weeks comment it out when everything is working, as it slows mysql down

Restart MySQL to make sure its picking up the new settings.

.. code-block:: sh

   sudo /etc/init.d/mysql restart
