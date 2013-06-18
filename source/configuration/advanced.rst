高级邮件服务器
=======================

Now lets extend this setup with more useful content checks , security and user interfaces.

Content Checks (Anti spam & anti virus)
----------------------------------------

Amavisd-new
^^^^^^^^^^^^^

Amavisd ties together all the different ways of checking email content for spam and viruses.
Install amavids-new

.. code-block:: sh

   sudo aptitude install amavisd-new

The defaults are pretty good and also the ubuntu documentation is pretty clear, and recommended.

Here is a tweaked version of it:

Initially we will not enable spam or virus detection! This is so we can get amavis set up to receive, check and pass on emails before we go on and over-complicate it.

All of amavis' configuration files are in /etc/amavisd. They are now spread across several files in conf.d. Debian and Ubuntu defaults are now very sensible and spread into seperate files.

.. code-block:: sh

   cd /etc/amavis/conf.d

01-debian defaults are fine.

Have a look at

.. code-block:: sh

   less 05-domain_id

but dont change anything in it.

Have a look at

.. code-block:: sh

   less 05-node_id

but dont change anything in it.

Have a look at

.. code-block:: sh

   less 15-av_scanners

but dont change anything in it.

Edit content check file

.. code-block:: sh

   sudo vi 15-content_filter_mode


.. code-block:: ini

   Comment out both virus and spam scans. (Default).
   # #@bypass_virus_checks_maps = (
   #   \%bypass_virus_checks, \@bypass_virus_checks_acl, \$bypass_virus_checks_re);
   # @bypass_spam_checks_maps = (
   #   \%bypass_spam_checks, \@bypass_spam_checks_acl, \$bypass_spam_checks_re);

Have a look at

.. code-block:: sh

   less 20-debian_defaults

and

.. code-block:: sh

   less 21-ubuntu_defaults

but dont change anything in them.

25-amavis_helpers defaults are fine.

30-template-localization defaults are fine.

Edit user file

.. code-block:: sh

   sudo vi 50-user

In the middle insert:

.. code-block:: sh

   @local_domains_acl = qw(.);
   $log_level = 2;
   $syslog_priority = 'debug';
   # $sa_tag_level_deflt = 2.0; # add spam info headers if at, or above that level
   # $sa_tag2_level_deflt = 6.31; # add 'spam detected' headers at that level
   $sa_kill_level_deflt = 8.0; # triggers spam evasive actions
   # $sa_dsn_cutoff_level = 10; # spam level beyond which a DSN is not sent
   $final_spam_destiny = D_PASS;
   # $final_spam_destiny = D_REJECT; # default 
   # $final_spam_destiny = D_BOUNCE; # debian default 
   # $final_spam_destiny = D_DISCARD; # ubuntu default, recommended as sender is usually faked

We have now setup amavis to scan and pass along incomming email. Next we will setup postfix to talk to amavis.

.. code-block:: sh

   vi /etc/postfix/master.cf

Append these lines to the end of the file (make sure they are not already present). (Note the -o lines have spaces in front of them.

.. code-block:: ini

   amavis      unix    -       -       -       -       2       smtp
        -o smtp_data_done_timeout=1200
        -o smtp_send_xforward_command=yes
        -o disable_dns_lookups=yes
        -o max_use=20
   127.0.0.1:10025 inet    n       -       -       -       -       smtpd
        -o content_filter=
        -o local_recipient_maps=
        -o relay_recipient_maps=
        -o smtpd_restriction_classes=
        -o smtpd_delay_reject=no
        -o smtpd_client_restrictions=permit_mynetworks,reject
        -o smtpd_helo_restrictions=
        -o smtpd_sender_restrictions=
        -o smtpd_recipient_restrictions=permit_mynetworks,reject
        -o smtpd_data_restrictions=reject_unauth_pipelining
        -o smtpd_end_of_data_restrictions=
        -o mynetworks=127.0.0.0/8
        -o smtpd_error_sleep_time=0
        -o smtpd_soft_error_limit=1001
        -o smtpd_hard_error_limit=1000
        -o smtpd_client_connection_count_limit=0
        -o smtpd_client_connection_rate_limit=0
        -o receive_override_options=no_header_body_checks,no_unknown_recipient_checks
        
Also add the following two lines immediately below the "pickup" transport service:


.. code-block:: sh

   -o content_filter=
        -o receive_override_options=no_header_body_checks

and then added to main.cf

.. code-block:: sh

   sudo vi /etc/postfix/main.cf

.. code-block:: ini

   content_filter = amavis:[127.0.0.1]:10024

Enable scanning by ClamAV of amavis' temporary files.

.. code-block:: sh

   sudo adduser clamav amavis

This should be it to get amavis working. If emails are picked up by amavis and passed back to postfix then it looks okay. Only when finished testing do you proced to uncomment the anti virus and anti spam lines in

.. code-block:: sh

   sudo vi 15-content_filter_mode

.. code-block:: ini

   @bypass_virus_checks_maps = (
   \%bypass_virus_checks, \@bypass_virus_checks_acl, \$bypass_virus_checks_re);
   @bypass_spam_checks_maps = (
   \%bypass_spam_checks, \@bypass_spam_checks_acl, \$bypass_spam_checks_re);

But do that after the next section (SpamAssassin).

When things are working we will turn down logging level, and start bouncing/discarding spam.

.. code-block:: sh

   sudo vi /etc/amavis/conf.d/50-user

.. code-block:: ini

   @local_domains_acl = qw(.);
   $log_level = 1;
   $syslog_priority = 'info';
   # $sa_tag_level_deflt = 2.0; # add spam info headers if at, or above that level
   # $sa_tag2_level_deflt = 6.31; # add 'spam detected' headers at that level
   $sa_kill_level_deflt = 8.0; # triggers spam evasive actions
   # $sa_dsn_cutoff_level = 10; # spam level beyond which a DSN is not sent
   # $final_spam_destiny = D_PASS;
   # $final_spam_destiny = D_REJECT; # default 
   # $final_spam_destiny = D_BOUNCE; # debian default 
   $final_spam_destiny = D_DISCARD; # ubuntu default, recommended as sender is usually faked


Anti-Spam
-------------

SpamAssassin
^^^^^^^^^^^^^^^^^^^

安装:
sudo aptitude install spamassassin spamc
The default config of spam assassin is okay. You could refer to previous edition for more configuration options.
You do need to tell SpamAssassin to start smapd on boot.

.. code-block:: sh

   vi /etc/default/spamassassin

.. code-block:: ini

   ENABLED=1

One configuration option you could tweak is to enable Bayes and auto learning.

.. code-block:: sh

   vi /etc/spamassassin/local.cf

I read your email

Anti Virus
---------------

ClamAV
^^^^^^^^^^^

安装:

.. code-block:: sh

   sudo aptitude install clamav-base libclamav6 clamav-daemon clamav-freshclam

(Earlier vesions of Ubuntu may use libclamav5)
ClamAV does not need setting up. Configuration files are in /etc/clamav, but they are automatically generated, so do not edit.

By default freshclam, the daemon that updates the virus definition database, is run 24 times a day. That seems a little excessive, so I tend to set that to once a day.

.. code-block:: sh

   sudo dpkg-reconfigure clamav-freshclam
 
It will also ask if you want it to be daemon (yes) and which server is closest to you.

If needed, the command below will redefine the configuration with a lot of questions. Not needed unless you need to configure.

.. code-block:: sh

   sudo dpkg-reconfigure clamav-base


Postgrey
---------

安装:

.. code-block:: sh

   sudo aptitude install postgrey

默认的postgrey配置是可以的. 然而你需要告诉Postfix来使用它.

.. code-block:: sh

   sudo vi /etc/postfix/main.cf

然后编辑收件人限制:

.. code-block:: ini

   smtpd_recipient_restrictions = reject_unauth_pipelining, permit_mynetworks, permit_sasl_authenticated,
		reject_non_fqdn_recipient, reject_unknown_recipient_domain, reject_unauth_destination, 
		check_policy_service inet:[::1]:10023, permit

.. note:: 新版使用的是ipv6协议，所以地址是，inet:[::1]:10023，以前的是，inet:127.0.0.1:10023

你可以在/etc/postgrey调整白名单. 你可以通过调整/etc/default/postgrey调整postgrey配置. E.g. delay, auto whitelisting, or reject message.

.. code-block:: ini

   POSTGREY_OPTS="--inet=10023 --max-age=365"


You know have an advanced mail server. You can use this, but Id recommend continuing. However this is a good point to test the set up so far and to insert some data in the db.
Ive created an EC2 bundle for this stage: flurdy-amis/ubuntu-mail-server-spam.
