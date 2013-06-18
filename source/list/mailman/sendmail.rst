于sendmail集成
===================

方法1
------------

This method uses the mm-handler.
See also Method 2 which does not use the mm-handler

Assumption
The following has been tested using the following configuration

Mailman 2.1.9
Sendmail 8.13.1
CentOS release 4.5 (Final)
Attached mm-handler (most noticeable change is writing logs to syslog)
You are going to be using the server only for mailing lists and not for delivery to local addresses
Method
For the purpose of this document the server domain shall be called mailman.foo.com and it is a 'A' name entry in the DNS. 

 virtusertable
 add the following in your /etc/mail/virtusertable 

	virtusertable
root@mailman.foo.bar    admin@foo.bar 
this shall redirect any email to root on the server to be redirected to the email admin on the mail server. 








 local.m4
local mailer is used by sendmail to deliver mails; however since we are not using the server to host any emails we don't need it.  We can change it to pass all the mails to mm-handler. Edit /usr/share/sendmail-cf/mailer/local.m4 

	local.m4
at the end of the file look for

Mlocal,    ...

and replace it with

Mlocal,    P=/etc/mail/mm-handler, F=rDFMhlqSu, S=EnvFromL, R=EnvToL/HdrToL,
             T=DNS/RFC822/X-Unix, U=mailman:mail,
             A=mm-handler $h -j $j -d $u
	Warning
Make sure that there is a tab after Mlocal, and not spaces.  Otherwise it might not work
	Warning
This Mlocal is modified so that sendmail passes the hostname using -j parameter.  Also a -d parameter has been added before $u.
This assumes that mm-handler is installed in /etc/mail/mm-handler.  You change it to the location where mm-handler is installed/copied.
U=mailman:mail shall run mm-handler as user:mailman, group:mail.  Change it to one that your mailman installation expects.
 mm-handler
Copy the file into /etc/mail/ directory (or where ever your installation of sendmail is).

	
Make sure that the path in your local.m4 is the same as the one where you are copying the mm-handler file
chmod ug+x mm-handler
This shall make the script executable by user and group. 

chown root:mail mm-handler&nbsp;This changes the group to mail.  However you should make it that specified in local.m4.
Now edit mm-hanlder and make sure that.

perl path is correct.
$MMWRAPPER is referring to right mailman configuration.
$MMLISTDIR is referring to the correct directory.
$SEND_MAIL is referring to correct program. 
	Warning
$SEND_MAIL is specific to the mm-handler attached to this document
After configuring the variables in mm-handler run it on the console.  This should generate log entries in your syslog.  In this case look for /var/log/maillog or /var/log/messages or wherever your system is configured to log these subsystems (refer to syslog.conf). 
Month&nbsp; DD HH:MM:SS mailman mm-handler\[32284\]: running as root:root
Month&nbsp; DD HH:MM:SS mailman mm-handler\[32284\]: running as root. For security reasons you should configure sendmail.mc to run the mailer as non-root
Month&nbsp; DD HH:MM:SS mailman mm-handler\[32284\]: #ARGV = \-1 ARGV = &nbsp;
Month&nbsp; DD HH:MM:SS mailman mm-handler\[32284\]: No arguments passed, doing nothing

If any of the configuration parameters are not correct i.e. correct file is not there or directory doesn't exist then you should see error messages like
Month DD HH:MM:SS mailman mm-handler\[3402\]: can not find /path/to/mailman.&nbsp; Please configure

Month DD HH:MM:SS mailman mm-handler\[3408\]: can not find /path/to/lists.&nbsp; Please configure

Once mm-handler can run with no errors.  Now you can try to send emails the server and see how it behaves.  You can see the log output to verify that emails are being handled properly.  Once you are satisfied with the configuration, you can change the syslog.conf and change the log level of mail to level 4 e.g
mail.4			 /path/to/maillog

This will log error and warning messages to your log file that can be used for administration purposes. 

方法2
--------------

This method uses a 'Postfix' workaround.

See also Integrating Mailman with Sendmail - Method 1 which uses mm-handler.

 Introduction
To be read in conjunction with the mailman installation instructions at
<http://www.list.org/mailman-install/index.html>
and the post by Ed Greenberg at
<http://mail.python.org/pipermail/mailman-users/2004-June/037518.html>

Step 1 - Installation requirements
I'm using Apache2, Mailman 2.19 and FreeBSD4 and Python 2.4

Step 2 - Set Up Your System
As root

#adduser
I used -> user: mailman, group:mailman, password: n

Step 3 - Build and Install Mailman
create Installation Directory (as root)

su# cd /usr/local/
su# mkdir mailman
su# chown mailman mailman
su# su mailman
su# cd mailman
su# chgrp mailnull .
su# chmod a+rx,g+ws .
Now, go to the directory where you have downloaded mailman

su# cd /usr/home/xxxxx/mailman-2.1.9
Change back to user root

su# su root
You can ascertain the correct option for --with-mail-gid from /etc/
mail/sendmail.cf

su# grep "DefaultUser" /etc/mail/sendmail.cf
=> #O DefaultUser=mailnull
So use mailnull
 
su# su root
su# make clean
su# configure \--with-mail-gid=mailnull
I'm on FreeBSD so use this command

su# make DIRSETGID=: install
Now check permissions

su# cd /usr/local/mailman
su# bin/check_perms \-f
My webserver runs as nobody (check httpd.conf on your system to confirm)

su# grep "User " /usr/local/apache2/conf/httpd.conf
=> User nobody
 
su# cd archives
su# chown nobody private
su# chmod o-x private
Step 4 - Check your installation
su# cd /usr/local/mailman
su# bin/check_perms \-f
Step 5 - Set up your webserver
Add this to your httpd-vhosts.conf, or httpd.conf depending on which 
version of Apache you are using

<VirtualHost \*:80>
ServerName lists.practiceimprovement.com.au
TransferLog /dev/null
DocumentRoot /usr/home/lists/htdocs/
ScriptAlias /mailman/ /usr/local/mailman/cgi-bin/
Alias&nbsp;&nbsp; /pipermail/ /usr/local/mailman/archives/public/
</VirtualHost>
and restart apache

Step 6 - Integrating sendmail and mailman
Integrating sendmail and mailman

mm-handler would not work for me (after considerable amount of time trying) - I think because by server was medicine.net.au and the address I wanted to use was practiceimprovement.org.au, even though practiceimprovement.org.au was correctly set up on dns to be delegated to the right server.

So I used Ed Greenberg's clever approach at
<http://mail.python.org/pipermail/mailman-users/2004-June/037518.html>
which seems better to me anyway.

Note: On Freebsd I first had to build sudo!

create the file /usr/sbin/mailman.aliases c

su# pico /usr/bin/mailman.aliases
containing the lines below =>

/bin/cp /usr/local/mailman/data/aliases /etc/mail/mailman.aliases
/usr/bin/newaliases
Note: I spent a lot of time bug testing as I used 'cp' in the 
mailman.aliases script rather than /usr/cp
(it worked from the command line but not the web interface)

make it executable

su# chmod 755 /usr/sbin/mailman.aliases
Change your sendmail.cf file to include the new alias file
(for me, add this to medicine.net.au.mc)
define(`ALIAS_FILE', `/etc/mail/aliases,/etc/mail/mailman.aliases')
(and then as root)

su# /etc/mail/make install restart
Update your sudoers file ('nobody' is the user apache runs under - 
check your httpd.conf)

su# visudo
add=>
nobody ALL= NOPASSWD: /usr/local/sbin/mailman.aliases
mailman ALL= NOPASSWD: /usr/local/sbin/mailman.aliases
Also, if you have

Defaults requiretty
in the sudoers file, you need to remove or comment it.

Step 7 - Review your site defaults
my mm_cfg.py has these added

DEFAULT_EMAIL_HOST = 'practiceimprovement.com.au'
DEFAULT_URL_HOST = 'lists.practiceimprovement.com.au'
DEFAULT_URL_PATTERN = 'http://%s/mailman/'
add_virtualhost(DEFAULT_URL_HOST, DEFAULT_EMAIL_HOST)
IMAGE_LOGOS = '/images/'
MTA='Postfix'
POSTFIX_ALIAS_CMD = '/usr/local/bin/sudo /usr/local/sbin/
mailman.aliases'
POSTFIX_STYLE_VIRTUAL_DOMAINS = \[\]
Steps 8 - 15 


Follow the rest of the instructions from step 8 on at ->
<http://www.gnu.org/software/mailman/mailman-install/index.html>

Testing
When you create a list using /bin/newlist, you should find that the file /etc/mail/mailman.aliases has been created / updated.
(I had some permissions to sort out in a few directories before it all worked smoothly)
The error log is helpful at /usr/local/mailman/logs/error

These commands can be helpful for troubleshooting, after you have successfully created a testlist and subscribed yourself to that list

To test mailman

su# echo "From: tony@pi.com.au
To: testlist@pi.com.au
Subject: Happy New Year
 
test mail body
" \| /usr/local/mailman/mail/mailman post testlist
If that works, use this to test your sendmail/mailman integration

su# echo "From: tony@pi.com.au
To: testlist@pi.com.au
Subject: Merry Xmas
 
test mail body
" \| /bin/sendmail \-tony@pi.com.au testlist@pi.com.au
Hope this helps someone and would welcome corrections or improvements.
