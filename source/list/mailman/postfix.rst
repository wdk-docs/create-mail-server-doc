集成于postfix
==================

This instruction set is written for those who wish to integrate Mailman with the postfix MTA.  The documentation currently available does not cover in detail how to use virtual hosts and MySQL-based aliases.

Please note: The instructions here are based on the Ubuntu 10.10 Linux distribution, loaded on a VPS.  Specific file locations may differ.

Before beginning, postfix should be installed and working correctly.  If you are using or plan to use virtual hosts, set this up prior to installing and configuring Mailman.

Normal postfix Integration
If you do not have any subdomains or do not plan on using virtual hosts within postfix, follow the steps below to integrate Mailman with your system.

Add this to the bottom of the $prefix/Mailman/mm_cfg.py file:

MTA = 'Postfix'
Please note that depending on your specific distribution, this setting may be set within $prefix/Mailman/Defaults.py.
Run the following command to initialize your mailman aliases:

$prefix/bin/genaliases
The command creates two files: aliases and aliases.db. These files are usually created within $prefix/data, but may be located elsewhere, depending on your configuration. They may sometimes be found within /var/lib/mailman/data.
Make sure that the owner and group owner of these files are set to mailman
postfix with Virtual Domains (hash map)
Let's assume you have several domains (in this case example1.com and example2.net) and you plan on hosting mailman on lists.example1.com and lists.example2.net.  You will have to select a default domain you want to host mailman on.  It doesn't really matter which one you choose but remember that it will be the one where everyone will come to administer their lists.  In this case, I will select lists.example1.com

Within $prefix/Mailman/mmcfg.py file, add the following lines below the MTA line you inserted above:

POSTFIX_STYLE_VIRTUAL_DOMAINS = ['example1.com', 'example2.net']
VIRTUAL_MAILMAN_LOCAL_DOMAIN = 'localhost'
Change DEFAULT_EMAIL_HOST and DEFAULT_URL_HOSTto match your default domain:

#-------------------------------------------------------------
# Default domain for email addresses of newly created MLs
DEFAULT_EMAIL_HOST = 'lists.mikesoh.com'
#-------------------------------------------------------------
# Default host for web interface of newly created MLs
DEFAULT_URL_HOST   = 'lists.mikesoh.com'
Save and close mmcfg.py and run $prefix/bin/genaliases, taking care to make sure that the resulting alias files are readable by postfix.
Next, modify your postfix configuration to include mailman aliases.  Within main.cf, add the resulting alias files:

virtual_alias_maps = mysql:/etc/postfix/virtual/existing_aliases.cf,
         hash:/var/lib/mailman/data/virtual-mailman
alias_maps = hash:/etc/aliases, hash:/var/lib/mailman/data/aliases
	Warning
The above additions to virtual_alias_maps and alias_maps are not used by postfix_to_mailman.py and can potentially cause conflicts within Postfix over delivery of mail to Mailman.

The GNU-Mailman project recommends the methods outlined in section 6.1.1 and section 6.1.2 of the Installation Manual for integrating Postfix and Mailman with virtual domains.
Save and close main.cf. Open the master.cf file. If not already included, add the mailman service to the end of the file:

# ==========================================================================
# service type  private unpriv  chroot  wakeup  maxproc command + args
#               (yes)   (yes)   (yes)   (never) (100)
# ==========================================================================
mailman   unix  -       n       n       -       -       pipe
  flags=FR user=list argv=/usr/lib/mailman/bin/postfix-to-mailman.py
  ${nexthop} ${user}
Save and close master.cf. Open your transport file. By default, this file should be located within the same directory as your main.cf and master.cf files. Add the following lines:

example1.com   mailman:
example2.net   mailman:
Run postmap -v on the transport file.
Restart mailman and postfix
You will not be able to create lists using the web interface. New lists must use the $prefix/bin/newlist command.
