扩展
============

Please refer to previous edition for how and why you can extend this mail server.

By now you should have a fully working system. No point extending and complicating it untill then. What next? There are many ways to extend the server, to create your own powerfull customized version.

Some of these sections can be brief as they are not core to this howto.

远程 MX 邮件备份
------------------------

With MX backup loosing emails are unlikely.

Normally if someone sends an email destined for you, their server will try and connect to your server.    If it can't reach your server for whatever reason ( it is down, dns issues, there is network problems, or just too busy ), the other server will back off and try again in a bit. How many and for how long it will try again is determined by the sending server. Some of them are not very patience, and it will report undelivered after only a few attempts. So you would have lost that email.

If you had specified a backup MX, this email may not have been lost. Upon first failure to connect to your server, the sender would see if there is any alternative server to send to. So it connects to your backup mx server. This server spools and queues your message and will try at intervals to send the message to you. It too will though eventually give up.

What is the difference? Simple, you (or whoever controls the backup mx ) is in control how long and often to try connecting to your machine. So if you have a reasonable values and your server is not down for weeks, no mail is lost.

How to implement it? First edit the DNS records again, and add a backup mx with a higher value.

.. code-block:: ubuntu

   # your server details
   domain.tld   IN   MX   10   your.mailserver.name.tld
   # new backup server
   domain.tld   IN   MX   20   your.backupserver.name.tld

Now presuming the other backup mx is a postfix server identical to this, or you are backuing up someone else's server; Go into mysql and create this tables:

.. code-block:: mysql

   CREATE TABLE `backups` (
   `pkid` smallint(6) NOT NULL auto_increment,
      `domain` varchar(128) NOT NULL default '',
      `transport` varchar(128) NOT NULL default ':[]',
      `enabled` smallint(6) NOT NULL default '1',
      PRIMARY KEY  (`pkid`),
      UNIQUE KEY `domain` (`domain`)
   );

Then still on the backup server, edit main.cf and add these:

.. code-block:: ini

   relay_domains = mysql:/etc/postfix/mysql_backups.cf
   transport_maps = mysql:/etc/postfix/mysql_transport.cf

You may choose to have this as the last line in the file, as you may use small cron jobs to modify this ip address, if you don't have a permanent static address. It should contain your IP addres, hence if you do not have a very static IP address, that you need to automatic editing if the postfix file.

.. code-block:: ini

   proxy_interfaces = 1.2.3.4

If someone comes with a better way, then let me know.

Next create this file /etc/postfix/mysql_backups.cf

.. code-block:: ini

   user=mail
   password=apassword
   dbname=maildb
   table=backups
   select_field=domain
   where_field=domain
   hosts=127.0.0.1
   additional_conditions = and enabled = 1

Next create this file /etc/postfix/mysql_transport.cf

.. code-block:: ini

   user=mail
   password=apassword         
   dbname=maildb
   table=backups      
   select_field=transport
   where_field=domain
   hosts=127.0.0.1
   additional_conditions = and enabled = 1

You noticed I added a transport lookup. This is a field in both the domain and the backup tables. In domains it is used to determine how to deliver the email, ie either virtual (correct) or local (not used in this howto). When backing up servers, your also need to specify in the transport field how to connect to the correct servers.

Say you are backiup for a friends server, mail.friend.com, for the domains of friend1.com and friend2.com. So you should insert this into your backup table.

.. code-block:: sql

   INSERT INTO backups (domain,transport)
   VALUES ('friend1.com' , ':[mail.friend.com]' ),
   ('friend2.com' , ':[mail.friend.com]' );

The :[] tells to connect directly to this server, not doing any more look ups for valid MX servers.

This shouls now work fine. Further tweaking of the queue values, review these and modify as appropiate. Shorter warning times are good for the sender, so that they realise the email has not arrived yet, but may also be annoying. Tradeoffs.. Look in the first main.cf configurations for ways to do so.

继发收件人查找
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Unfortunetly spammers are using backup mx as a way to saturate the networks with invalid emails, known as backscatter mail.

They simply lookup a domain's MX servers and connect directly to one of the lower priority servers whom may be just a backup mx. This server if configured as above will not check for valid addresses aliases but will accept and queue all emails for the domain's it is configured as a backup mx for. These will then be delivered by the server later to the primary MX server, whom will then maybe reject them as the aliases are not valid. However the sender addresses are often invalid and a long trail of reject messages to and forth around the net follows...

To avoid this you can enable relay recipient lookup in Postfix.

Edit /etc/postfix/main.cf and add:

relay_recipient_maps = mysql:/etc/postfix/mysql_relays.cf

Then create a new file /etc/postfix/mysql_relays.cf

.. code-block:: ini

   user=mail
   password=apassword
   dbname=maildb
   table=relays
   select_field=recipient
   where_field=recipient
   hosts=127.0.0.1
   additional_conditions = and enabled = 1
   
Then add the following MySQL table:

.. code-block:: mysql

   CREATE TABLE `relays` (
   `pkid` smallint(6) NOT NULL auto_increment,
   `recipient` varchar(120) NOT NULL default '',
   `enabled` tinyint(1) NOT NULL default '1',
   `status` varchar(10) NOT NULL default 'OK',
   PRIMARY KEY  (`pkid`),
   UNIQUE KEY `recipient` (`recipient`)
   );
   
If the relay_recipient_maps setting is set, then postfix will reject all email addresses not specificed in this table. As with many postfix lookups, it will recursively search for a match from the full address.
In the following examples, emails to john@example.com are the only emails that will be accepted for the whole example.com domain. 
However for @example.org all emails will be accepted for backup, except any for support@example.org which will be rejected.

.. code-block:: mysql

   insert into relays (recipient,status) values 
      ('john@example.com','OK'),
      ('support@example.org','REJECT'),
      ('@example.org','OK');

本地文件备份
----------------------

Here is rough backup script to backup your configurations and mail folders. You may want to backup the folders seperatly as they can quickly grow to GBs. Adding this to a cronjob automates this process. Be aware that you should stop postfix and courier while backing up the mail folders. And that if they have grown large, that this may take some time.

.. code-block:: sh

   tar czf mail-config.xxxxx.tgz /etc/postfic /etc/courier /etc/spamassassin /etc/clamav /etc/amavis /etc/mysql/my.cnf
   tar czf mail-fold.xxxx.tgz /var/spool/mail/virtual
   mysqldump -u mail -papassword -t maildb > data.sql
   mysqldump -u mail -papassword -d maildb > schema.sql
   tar czf mail-data.xxx.tgz schema.sql data.sql
   tar cf mail.xxxxx.tar  mail-*.xxxxx.tgz 

You may combine a full backup with a intermediate update of what has changed recently only.

.. code-block:: sh

   tar --newer-mtime "2005-01-01"

发件人ID和SPF
----------------------

Further security features is using Microsoft's Sender ID or Pobox's SPF. I'd use SPF as there is much argument over Sender ID.

spf.pobox.com

www.microsoft.com/mscorp/safety/technologies/senderid/

SPF should limit who can send mail on behalf of your domains, and is an open design. I do recommend SPF, with some reservations, detailed below.

While Microsoft is not always entirely evil, as sometimes they do nice things and make some useful software, I would prefer not to be locked into their Sender ID technology.

SPF configuration

The pobox site has some nice SPF generation tools to setup your SPF configuration. Probably best to use theirs.

But the way I have my setup, is generally one domain with detailed SPF, then all other domains just with an SPF alias to it. e.g:

Main domain DNS TXT field:

"v=spf1 a mx a:myserver.example.com include:aspmx.googlemail.com include:gmail.com ~all"

The important elements are:

I list the mail servers and websites associated with this domain (the a and mx bit).

I then specifically list the name of a server I may send mail from applications automatically using addresses within this domain.

As you can see I also use Google Apps with this domain, thus tell SPF to also allow all mail servers associated with google mail.

Then for most of the other domains I would use this DNS TXT field:

"v=spf1 a mx include:example.com ~all"

The important elements are:

I list the mail servers and websites associated with this domain

Then I tell SPF to also allow all mail servers associated with my main domain (example.com).

And for all these I use ~all!

Ps. Some domains I have added an even stricter SPF, as these are domains that will never send an email.

SPF problem

It is worth noting about SPF, that you should leave the decision to whether to reject or allow the email to the mail servers. Therefore using -all instead of ~all is not a good choice. Leave it to the SPAM scoring by the receiving server, like SpamAssasin does it. You then minimise the risk of false positives.

One of the reason I do discourage -all use, is that SPF has a distinct problem:

It does not like email forwarding or use of backup MX!

Consider this: Your address of lulu@hoopa.com sends a joke email to a few friends. One of these is trixie@bellbell.org.

Trixie's email address is actually an alias and forwards the email to her private webmail account on hotmailnot.com.

Now if your domain, hoopa.com, have a strict SPF set up, which only allows emails to be sent by its mail server. And you/the mail admin has added -all to the SPF, which tells other server to reject emails not from your server. This you think makes sense, spammers can not use your domain for spoof emails.

So what happens: bellbell.org receives the email from lulu, and possible checks the SPF, which is OK, and forwards it on to hotmailnot.com.

However if hotmailnot.com also checks SPF, it will receive the email from bellbell.org, check the SPF to see bellbell.org's mail server is allowed to send emails on behalf hoopa.com. SPF will say No!, and with the -all, hotmailnot.com email server will reject the email!

2nd scenario if lulu email trixie directly at hotmailnot.com, but hotmailnot.com main mail server was down, and email was sent to the backup mx server. When the main server came online again, and the backup spooled the email back to it, the SPF would again fail as the hoopa.com's SPF would not mention hotmailnot.com backup mx as an allowed mail server.

Solution: 

Of course you can not list all possible forwarding / backup mx email server that your domain's users might at some point email!

I simple just use the ~all option. Which simple say it is not the expected server, but probably ok. 

And if this is added to a scoring by the receiver, then the accumulated spam score might be enough to reject dodgy emails.

垃圾邮件报告
----------------------

todo

Reporting spam to Pyzor, Razor and SpamCop, for collaboration in spam fighting.

More detail on SpamCop is here.

pyzor.sourceforge.net

razor.sourceforge.net

白名单/黑名单
----------------------

todo

You can implement white and black lists to explicitly allow or block domains and users.

You have already visited the option of a blackhole list of known open relays in the postfix configuration.

You can implement further lists inside Postfix or SpamAssassin. Amavisd-new already has a few well known white/black listed items in its config files. SpamAssissin also as a feture to automaticly learn white lists.

PGP & S/MIME
----------------------

Adding support for GnuPG and S/MIME increases indiviual security.

This is not implemented on the postfix server side, as this totally a client side option.

However SquirrelMail has a GnuPG option. It is a plugin that can be downloaded from their website. Which can then be enabled via SquirrelMail's config script.

Here is how to create a GnuPG key pair.

.. code-block:: sh

   # check you have not already got a key
   gpg --list-keys
   # then create one
   gpg --gen-key

To import GnuPG into Evolution; in your settings/preferences edit your account settings and add you private key under the security tab. The private key is found via listing the GnuPG keys as above, then it is the 8 characters after the "sub 1024g/" bit of you key.

To use GnuPG with   Thunderbird you need to install EnigMail.

S/MIME is another way to encrypt and/or sign messages. You can create you own certificate or use known organizations like Thawte. (Thawte was originally set up by the Ubuntu founder)

搬迁通知
----------------------

If people change addresses, a bounced message stating so if people send email to the old address is quite useful. To implement this in postfix, frst create a lookup table in the database.

.. code-block:: mysql

   CREATE TABLE `relocated` (
   `pkid` smallint(6) NOT NULL auto_increment,
   `oldadr` varchar(128) NOT NULL default '',
   `newadr` varchar(128) NOT NULL default '',
   `enabled` tinyint(1) NOT NULL default '1',
   PRIMARY KEY  (`pkid`),
   UNIQUE KEY `oldadr` (`oldadr`)
   ) ;
   
Then add this to /etc/postfix/main.cf

.. code-block:: ini

   relocated_maps = mysql:/etc/postfix/mysql_relocated.cf

The create this file /etc/postfix/mysql_relocated.cf

.. code-block:: ini

   user=mail
   password=apassword
   dbname=maildb
   table=relocated
   select_field=newadr
   where_field=oldadr
   hosts=127.0.0.1


Then if pete@domain1.com has changed address to pete.jones@another.org:

INSERT INTO relocated (oldadr,newadr)VALUES

('pete@domain1.com','pete.jones@another.org');

If anyone sends an email to pete@domain.com, they will get a message back stating he has changed address to pete.jones@another.org.

Pop-before-SMTP
----------------------

If SASL didn't work, or you are using clients which dont support it, the Pop-Before-SMTP is an easy way around that issue, so that people externally can still securly send mail via your server.

Refer to my 2nd edition on Pop-before-SMTP   setup.

管理软件
----------------------

todo

Trying out a few admin software might make you life easier, if phpMyAdmin gets to crude. Quick search
More to come later.

自动回复
----------------------

.. todo:: Postfix have now features to auto reply to an email, while still delivering it to its alias.


块地址
----------------------

If you use catch alls, which are useful for some domains, then eventually some addresses will be target for spam. You can then either stop the catch all, or stop indivdual addresses.

By implementing a lookup and adding this restriction to smtpd_recipient_restrictions accomplises this.

check_recipient_access mysql:/etc/postfix/mysql_block_recip.cf,

.. code-block:: ini

   smtpd_recipient_restrictions = permit_mynetworks, permit_sasl_authenticated, \
   check_recipient_access mysql:/etc/postfix/mysql_block_recip.cf, \
   reject_non_fqdn_recipient, reject_unauth_destination, \
   check_relay_domains

Beware of the order is important here, if any options says ok before check_recipient_access it will ignore it.

Next create mysql_block_recip.cf to lookup addresses. Either create a another table, or add a blocked field to aliases table.

油门输出
----------------------

.. todo:: For some users with restrictions on bandwidth, you may wish to control how much mail is sendt out. Postfix has long refused to implement these features, out of ideolocial beliefs that mail servers should not be restricted. However there are some ways around this. More to come later.

邮件列表
----------------------

Rich Brown has written a howto on adding Mailman, a mail list program, to my howto.    Click here to read it.

Do note it is not part of my howto, so do not contact me regarding it. And although I think it is fine, I can't guarantee it will work.

If you do need assistance or need to talk about it, contact Rich via his howto or use the forums for this howto.

If you want a simple mailling list, it can be implemented by simply seperating aliases in the destination field in the aliases table with a comma.

.. code-block:: sql

   INSERT INTO aliases (mail,destination) VALUES
   ( 'listof@domain.com' , 'john@ppp.com,vic@domain.com,jj@somewhere.tld' );

Google Apps / GMail
----------------------

I have for various reasons integrated some Google Apps hosted domains into my mail server. And you can still have good control over the addresses by using your server with Google Apps.

More information on Google Apps.

为什么

Some already have their domain's email hosted with Google.

Some people prefer Google's web based interface.

Temporary Migrations.

Include Google's security features on top of yours.

怎么做

Options

The easiest and simples solution is not to have a domain MXed to your server, and simply alias email to those domains. eg All email to joeblogs.co.uk hosted on your server are forwarded to joeblogs.com hosted with google.

You may set up your own server to simple be a mail server backup (mx) for a domain hosted with google. If you are the first priority in the MX details of the DNS, you still have some control, but not all will obey the priority listing. E.g. spammers, but some valid senders as well.

However the one I use and the option where you are most in control is to keep you server as the only MX server in the DNS. And only forward certain aliases onto Google after all your servers checks. Other aliases and user can just use your mail server if you prefer. I will explain how to do this in the next steps.

DNS

You only put your mail server as the mx for the domain in question. Google will complain about this, as it will not be able to verify that email is setup correctly. Ignore this as it will still accept emails.

MySQL tables

You setup you aliases as normal. However you domain table needs tweaking. This is because otherwise your server will just forward the email to itself. You can actually specify aliases in the domain table.

Example

If for example:joe@bloggs.com wants to use gmail. mary@bloggs.com does not.

If not already configured as a backup mx:

Add a transport lookup to your /etc/postfix/main.cf file:

.. code-block:: ini

   transport_maps = mysql:/etc/postfix/mysql_transport.cf

Then create /etc/postfix/mysql_transport.cf file:

.. code-block:: ini

   user=mail
   password=apassword
   dbname=maildb
   table=backups
   select_field=transport
   where_field=domain
   hosts=127.0.0.1
   additional_conditions = and enabled = 1

Assuming there are no bloggs.com data in any tables (domain,alias,users,relays,backups):

.. code-block:: sql

   insert into domains (domain,transport) values 
      ('joe@bloggs.com','smtp:[aspmx.l.google.com]:587'),
      ('bloggs.com','virtual:');
   insert into aliases (mail,destination) values 
      ('joe@bloggs.com','joe@bloggs.com'),
      ('mary@bloggs.com','mary@bloggs.com');
   insert into users (id,name,maildir,crypt) values 
      ('mary@bloggs.com','mary','bloggs.com/mary',encrypt('maryspassword') );

The domains insert is the interesting one. The transport map lookup checks recursively for an alias match and will first look for user@domain before it looks at the general bloggs.com for which transport to use. The square brackets around aspmx.l.google.com indicates that this server will not lookup for mx settings for this domains DNS, but instead connect directly. (This can avoid never ending recursive lookups/relays)

Note if you have backup mx configured and chosen to enable relay recipient lookup to avoid backscatter mail spam, then you need to add your Google Apps users to the relays table:

insert into relays (recipient,status) values ('joe@bloggs.com','OK');

Refer to backup mx section for creation of this table.

TLS certificate

If you have set your server up to prefer TLS then you should add Google's signing authority to your server's root certificate list. Google used to use Thawte but now use Equifax.

Download the Equifax Secure Certificate Authority certificate from their website (the base-64 encoded):

wget http://www.geotrust.com/resources/root_certificates/certificates/Equifax_Secure_Certificate_Authority.cer;

You need to fix the line endings in this file by either using sed:

.. code-block:: sh

   sed -i 's/.$//' Equifax_Secure_Certificate_Authority.cer;

Or install a tiny util:

.. code-block:: sh

   sudo aptitude install tofrodos;

fromdos Equifax_Secure_Certificate_Authority.cer;

Put it into your certificate root folder:

.. code-block:: ini

   sudo chown root:root Equifax_Secure_Certificate_Authority.cer;
   sudo mv Equifax_Secure_Certificate_Authority.cer /usr/share/ca-certificates/mozilla/;
   cd /etc/ssl/certs;
   sudo ln -s /usr/share/ca-certificates/mozilla/Equifax_Secure_Certificate_Authority.cer .;

And then append it to the root list that postfix knows about:

.. code-block:: ini

   sudo su;
   cat Equifax_Secure_Certificate_Authority.cer >> ca-certificates.crt;
   exit;
   sudo service postfix restart;

Issues

There are some items you should consider when integrating Google Apps.

Privacy

First there is the privacy issue. This is the same as if you were using Google Apps only or GMail. Google can and will read your email. However probably not a person, but they will use it for commercial reasons, E.g. showing relevant ads. Some people really hate this part and refuse to use Google's mail products. However I trust them a little bit, and do use it.

Spam

If you forward spam, then consider your own servers reputation. Should be okay though.

SPF

If you use SPF for your domain, consider that both your server and google will receive and send mail on behalf of that domin. Adding
include:_spf.google.com

should cover it.

Google internally

Be aware Google think they host you domain. So if others inside google, or using google hosted apps or GMail, if they email you, the email may not go via your email server, but directly to the Google Apps for your domain. That could be an issue if not all aliases you have use Google Apps. This needs to be tested more though. Especially as it may only be an issue if Google's servers are part of you domains MXs. It may be worth adding aliases in your Google Apps admin for the non google apps adresses to some user whom can handle these?

Maildrop, spam folder and vacation messaging

Villu have documented swapping in Maildrop for virtual transport and automatically deliverin spam to a spam folder. (And links to a post about vacation messaging)
Please read his post here.

Squirrel Mail
----------------------

Using among others the https://help.ubuntu.com/community/Squirrelmail as an updated reference.

You need to copy a SquirrelMail configuration to apache.

.. code-block:: ini

   sudo cp /etc/squirrelmail/apache.conf /etc/apache2/sites-available/squirrelmail

And enable with this:

.. code-block:: ini

   sudo ln -s /etc/apache2/sites-available/squirrelmail /etc/apache2/sites-enabled/500-squirrelmail

Or as Florent recommends, use:

.. code-block:: ini

   sudo a2ensite squirrelmail

You may accept the default apache configuration where squirrelmail is folder in all sites. But I prefer virtual hosting. But you dont need to do these next steps.

.. code-block:: ini

   sudo vi /etc/apache2/sites-available/squirrelmail
Comment out the alias.

.. code-block:: apache

   # alias /squirrelmail /usr/share/squirrelmail

Uncomment the virtual settings., and insert your servers name.

.. code-block:: apache

   # users will prefer a simple URL like http://webmail.example.com
   DocumentRoot /usr/share/squirrelmail  
   ServerName webmail.example.com

If you have apache SSL enabled in apache, then you can also uncomment the mod_rewrite section for further security.

Reload apache to activate changes. First test if ok.

.. code-block:: sh

   sudo apache2ctl -t

Then reload it.

.. code-block:: sh

   sudo /etc/init.d/apache2 reload
   
You can now go toyourdomain.com/squirrelmail/ or mail.yourdomain.com if you chose virtual host. This should show a squirrel mail page. Log in wont work yet though.

Start configuring squirrel mail.

.. code-block:: apache

   sudo squirrelmail-configure
   
.. code-block:: sh

   Initially change nothing. You can customize more afterwards. You can browse, and exit sub menues by typing R.
   Type 2 to edit server settings. Type A to edit IMAP settings.
   Type 8 to edit server software. Enter courier.
   courier
   Now they say using TLS over localhost is a waste of time. But I do anyway. Type 7 to edit secure IMAP. Type
   Y
   to enable it.
   Type 5 to edit IMAP port. Enter
   993
   Type S to save your changes. Hit Enter.
   Type Q to exit.

You can now go to yourdomain.com/squirrelmail/ or mail.yourdomain.com if you chose virtual host. This should show a squirrel mail page. Log in will now work. (Except you may not have defined users, check data section. And they may not have received an email which also means you can not view any IMAP info.)

Please refer to previous edition for more detail. E.g. creating address books and user preferences.

蛮力
----------------------

Preventing Brute Force attack on your server.

First line of defence is the firewall. If they cant get to your server then they cant hack in. However to be a useful internet based server you have to expose some services, e.g. SMTP and maybe SSH.

Below are two widely used ways to protect yourself.

拒绝主机
-------------

Deny hosts is an effective tool to protect your SSH service from brute force attack. To install:

.. code-block:: sh

   sudo aptitude install denyhosts

Tweak in /etc/denyhosts.confs. Perhaps whitelist your ips to prevent accidentally locking yourself out... You do this via /etc/hosts.allow.

Read more in this thread for tweaks.

To protect your server against distributed attack, read about DenyHosts' synchronisation feature.

不禁止
--------------

fail2ban protects against a multitude of brute force attacks. Relevant to this guide is the protection for SMTP and IMAP. Follow this guide    for how to install & configure it.

建议?
----------------------

If you have any suggestions to other ways of extending a postfix server, then fire off a mail to me via the contact form further down.

(Or rather, Id prefer that you write down the extension, and let me know the link! :))
