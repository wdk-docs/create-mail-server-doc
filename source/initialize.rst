初始化
=================

Brief hints if you receive a ready setup machine (or EC2 AMI), and what then to check and to customize it to your setup.

* Stop services
* Restrict firewall
* Change passwords
* Check configurations
* Set machine name
* Certificates
* Start and test services
* Insert data
* Reload postfix
* Open firewall
* Test

停止服务
------------------

First stop services so they wont accidentally do something.

.. code-block:: sh

   sudo service spotfix stop
   sudo service courier-imap-ssl stop
   sudo service courier-imap stop
   sudo service courier-authdaemon stop
   sudo service mysql stop
   sudo service amavisd stop
   sudo service spamassassin stop
   sudo service clamav stop

限制防火墙
------------------

Check what the firewall rules are.

.. code-block:: sh

   vi /etc/shorewall/rules

Refer to the firewall settings. Restrict to just SSH access for now.

修改密码
------------------

Next the passwords needs to be changed. For both the system and mysql.

System passwords

Check which users are defined on the system.

.. code-block:: sh

   cat /etc/passwd

Apart from all the system ones, there should probably be none (if EC2 AMI) or just your user if it is a standard Ubuntu install. If there are some users, you need to change their passwords.

SSH Access

Next we check whom got SSH access. If there was any users defined, check their home folders for ssh keys.

.. code-block:: sh

   cat /home/username/.ssh/auth*

Remove any you do not expect to be there. Next check if and which specific users has been defined for SSH access in

.. code-block:: sh

   vi /etc/ssh/sshd

Usually this is fine.

MySQL 密码
------------------

First you need to change the root mysql user. If none has been set do this

.. code-block:: sh

   mysqladmin -u root password new_password

Otherwise do this and you will be prompted for the old password

.. code-block:: sh

   mysqladmin -u root password new_password -p

Then the default mail user as well. If you know the old password

.. code-block:: sh

   mysqladmin -u mail password new_password -p

Otherwise log into mysql as root:

.. code-block:: sh

   mysql -u root -p

Enter new root password specified above, then:

.. code-block:: mysql

   update mysql.user set password=password('apassword') where user='mail';
   
   flush privileges;

You may	need to revisit the top of MySQL section to re-grant the mail use rights on the database.

If you do not know the old root password, you have to restart mysql without grant rights. Google it... :)

Update postfix mysql configuration files with the new password.

.. code-block:: sh

   sudo vi /etc/postfix/mysql*

.. code-block:: ini

   password=apassword

Update courier's authmysql file with the new password as well.

.. code-block:: sh

   sudo vi /etc/courier/authmysqlrc

.. code-block:: ini

   MYSQL_PASSWORD apassword

If SASL is set up, then you need to update its passwords. First in postfix SASL file:

.. code-block:: sh

   sudo vi /etc/postfix/sasl/smtpd.conf

.. code-block:: ini

   sql_passw: aPASSWORD

Then on both lines in:

.. code-block:: sh

   sudo vi /etc/pam.d/smtp

.. code-block:: ini

   passwd=aPASSWORD

检测配置
------------------

You should scan the postfix, courier, etc. configurations to check if they match what you expect.

设置机器名字
-------------

Now you need to define your machine name, e.g. something like smtp.yourdomain.com. You need to define it in

.. code-block:: sh

   sudo vi /etc/mailname

And then your domain name in

.. code-block:: sh

   sudo vi /etc/postfix/main.cf

under the mydomain setting

.. code-block:: ini

   myorigin=yourdomain.com

It could also be smart to check what the unix hostname is specified as

hostname

This can be reset by

sudo hostname smtp.yourdomain.com.

All though this does not have to be the same as your postfix mail server name.	 You may want to speficiy some hosts in hosts file as well,

.. code-block:: sh

   sudo vi /etc/hosts

.. code-block:: ini

   127.0.0.1 localhost.localdomain localhost
   127.0.0.1 smtp.yourdomain.com smtp

证书
------------------

You could go along with the generated certificates (if they are there, default for Ubuntu). Or if you could create new ones with the correct machine name in them. Especially if this a mail server used by many, and authenticiy is important. Follow the TLS certificate instructions for Postfix and Courier.

启动并测试服务
------------------

Next you need to start your mail services and test them.

.. code-block:: sh

   sudo /etc/init.d/mysql start
   sudo /etc/init.d/spamassassin start
   sudo /etc/init.d/clamav start
   sudo /etc/init.d/amavisd start
   sudo /etc/init.d/postfix start
   sudo /etc/init.d/courier-imap-ssl start
   sudo /etc/init.d/courier-imap start
   sudo /etc/init.d/courier-authdaemon start

So test tjenestene via testing section.

插入数据
------------------

Insert your mail domains, aliases and users using the data section.

Some times there are test data already in the database. Remove them. E.g.;

.. code-block:: sh

   mysql -u mail -papassword maildb

.. code-block:: mysql

   delete from domains where domain = 'bar.com';
   delete from aliases where mail = 'foo@bar.com';

打开防火墙
-----------

Then open up the firewall, follow the world access bit in the firewall configuration. Voila. Up and running. Well we hope.
