MTA
========

Postfix
--------------

安装
^^^^^^^^^^^^^^^^^

sudo aptitude install postfix postfix-mysql

This will prompt you to choose type of email server. Select internet site It will also suggest a server name. Correct this if needed.

配置
^^^^^^^^^^^^^^^^^

You should put the name of your server in this file

.. code-block:: ini

   sudo vi /etc/mailname

Could be something like smtp.domain.name, where domain name obviously is replaced with your domain name.

Now will open the main postfix configuration file:

.. code-block:: sh

   sudo vi /etc/postfix/main.cf

Debian and Ubuntu already puts in some sensible default values in this file. You may need to comment some of them out if we put the same in as well.

设定服务器名字：

.. code-block:: ini

   # This is already done in /etc/mailname
   #myhostname= mail.example.com

Next is the origin which is the domain appended to email from this machine, this can be your full servername, or domain name.

.. code-block:: ini

   # myorigin=/etc/mailname
   myorigin=example.com

Then decide what the greeting text will be. Enough info so it is useful, but not divelge everything to potential hackers.

.. code-block:: ini

   smtpd_banner = $myhostname ESMTP $mail_name

Next you need to decide whether to send all outgoing mail via another SMTP server, or send them yourself. I send via my ISP's server, so it has to worry about the queing etc. If you send it yourself then you are not reliant on 3rd party server. But you may risk more exposure and accidentally be blocked by spam blockers. And it is more work for your server. Also many servers block dynamic dns hosts, so you may find your server gets rejected. However choose whichever you are comfortable with.

.. code-block:: ini

   # leave blank to do it yourself
   relayhost =
   # or put it an accessible smtp server
   relayhost = smtp.yourisp.com

Next is network details. You will accept connection from anywhere, and you only trust this machine

.. code-block:: ini

   inet_interfaces = all
   mynetworks_style = host

Next you can masquerade some outgoing addresses. Say your machine's name is mail.domain.com. You may not want outgoing mail to come from username@mail.example.com, as you'd prefer username@example.com. You can also state which domain not to masquerade. E.g. if you use a dynamic dns service, then your server address will be a subdomain. You can also specify which users not to masquerade.

# masquerade_domains = mail.example.com www.example.com !sub.dyndomain.com
# masquerade_exceptions = root

As we will be using virtual domains, these need to be empty.

.. code-block:: ini

   local_recipient_maps = 
   mydestination =

Then will set a few numbers.

.. code-block:: ini

   # how long if undelivered before sending warning update to sender      
   delay_warning_time = 4h 
   # will it be a permanent error or temporary
   unknown_local_recipient_reject_code = 450 
   # how long to keep message on queue before return as failed.
   # some have 3 days, I have 16 days as I am backup server for some people
   # whom go on holiday with their server switched off.
   maximal_queue_lifetime = 7d 
   # max and min time in seconds between retries if connection failed
   minimal_backoff_time = 1000s 
   maximal_backoff_time = 8000s 
   # how long to wait when servers connect before receiving rest of data
   smtp_helo_timeout = 60s 
   # how many address can be used in one message.
   # effective stopper to mass spammers, accidental copy in whole address list
   # but may restrict intentional mail shots.
   smtpd_recipient_limit = 16 
   # how many error before back off.
   smtpd_soft_error_limit = 3 
   # how many max errors before blocking it.
   smtpd_hard_error_limit = 12

Now we can specify some restrictions. Be carefull that each setting is on one line only.

.. code-block:: ini

   # Requirements for the HELO statement
   smtpd_helo_restrictions = permit_mynetworks, warn_if_reject reject_non_fqdn_hostname, 
      reject_invalid_hostname, permit
   # Requirements for the sender details
   smtpd_sender_restrictions = permit_mynetworks, warn_if_reject reject_non_fqdn_sender, 
      reject_unknown_sender_domain, reject_unauth_pipelining, permit
   # Requirements for the connecting server 
   smtpd_client_restrictions = reject_rbl_client sbl.spamhaus.org, 
      reject_rbl_client blackholes.easynet.nl, 
      reject_rbl_client dnsbl.njabl.org 
   # Requirement for the recipient address
   smtpd_recipient_restrictions = reject_unauth_pipelining, permit_mynetworks, 
      reject_non_fqdn_recipient, reject_unknown_recipient_domain, 
      reject_unauth_destination, permit
   smtpd_data_restrictions = reject_unauth_pipelining

Further restrictions:

.. code-block:: ini

   # require proper helo at connections 
   smtpd_helo_required = yes
   # waste spammers time before rejecting them
   smtpd_delay_reject = yes
   disable_vrfy_command = yes

Next we need to set some maps and lookups for the virtual domains.

.. code-block:: ini

   # not sure of the difference of the next two
   # but they are needed for local aliasing
   alias_maps = hash:/etc/postfix/aliases
   alias_database = hash:/etc/postfix/aliases
   # this specifies where the virtual mailbox folders will be located
   virtual_mailbox_base = /var/spool/mail/virtual
   # this is for the mailbox location for each user
   virtual_mailbox_maps = mysql:/etc/postfix/mysql_mailbox.cf
   # and this is for aliases
   virtual_alias_maps = mysql:/etc/postfix/mysql_alias.cf
   # and this is for domain lookups
   virtual_mailbox_domains = mysql:/etc/postfix/mysql_domains.cf
   # this is how to connect to the domains (all virtual, but the option is there)
   # not used yet
   # transport_maps = mysql:/etc/postfix/mysql_transport.cf

You can (as in my older editions) use a lookup for the uid and gid of the owner of mail files. But I tend to have one owner(virtual), so instead add this:

.. code-block:: ini

   virtual_uid_maps = static:5000
   virtual_gid_maps = static:5000

You need to set up an alias file. This is only used locally, and not by your own mail domains.

.. code-block:: sh

   sudo cp /etc/aliases /etc/postfix/aliases
   # may want to view the file to check if ok.
   # especially that the final alias, eg root goes
   # to a real person
   sudo postalias /etc/postfix/aliases

Next you need to set up the folder where the virtual mail will be stored. This may have already been done by the apt-get. And also create the user whom will own the folders.

.. code-block:: sh

   # to add if there is not a virtual user
   sudo mkdir /var/spool/mail/virtual
   sudo groupadd --system virtual -g 5000
   sudo useradd --system virtual -u 5000 -g 5000
   sudo chown -R virtual:virtual /var/spool/mail/virtual

.. note:: If using Amazon ec2 you may want to move the mail spool to /mnt or an EBS location. You will need to symlink correctly afterwards.

Postfix's MySQL 配置
------------------------------
Next we need to set up the files to access the lookups via the database. We will only set up a few now, and the rest later when/if needed:

Edit(create) how to find the users mailbox location

.. code-block:: bash

   sudo vi /etc/postfix/mysql_mailbox.cf

.. code-block:: ini

   user=mail
   password=mailPASSWORD
   dbname=maildb
   table=users
   select_field=maildir
   where_field=id
   hosts=127.0.0.1
   additional_conditions = and enabled = 1

Create how to find the email alias:

.. code-block:: bash

   sudo vi /etc/postfix/mysql_alias.cf

.. code-block:: ini

   user=mail
   password=mailPASSWORD
   dbname=maildb
   table=aliases
   select_field=destination
   where_field=mail
   hosts=127.0.0.1
   additional_conditions = and enabled = 1

Create how to find the domains:

.. code-block:: bash

   sudo vi /etc/postfix/mysql_domains.cf

.. code-block:: ini

   user=mail
   password=mailPASSWORD
   dbname=maildb
   table=domains
   select_field=domain
   where_field=domain
   hosts=127.0.0.1
   additional_conditions = and enabled = 1

If you specify an ip in hosts, (as opposed to 'localhost') then it will communicate over tcp and not the mysql socket. (chroot restriction). Ps. remember to replace the passwords with your chosen mail user password.
