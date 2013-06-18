数据
=====

添加用户和域
----------------------

So we got a fully set up mail server... Well no, there is no users, domains, no nothing!

Okay, first you need add some default data, some which are required, some which make sense.
Then we'll add your own users and domains.

需要域和用户
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

首先本地邮箱需要域

.. code-block:: mysql

   # Use phpMyAdmin or command line mysql
   INSERT INTO domains (domain) VALUES
       ('localhost'),
       ('localhost.localdomain');
   Then some default aliases. Some people say these are not needed, but I'd include them.
   INSERT INTO aliases (mail,destination) VALUES
       ('postmaster@localhost','root@localhost'),
       ('sysadmin@localhost','root@localhost'),
       ('webmaster@localhost','root@localhost'),
       ('abuse@localhost','root@localhost'),
       ('root@localhost','root@localhost'),
       ('@localhost','root@localhost'),
       ('@localhost.localdomain','@localhost');

然后一个根用户.

.. code-block:: mysql

   INSERT INTO users (id,name,maildir,crypt) VALUES 
       ('root@localhost','root','root/',encrypt('apassword') );

Domains 和 users
^^^^^^^^^^^^^^^^^^^^^^^^

Now lets add some proper data.
Say you want this machine to handle data for the fictional domains of "blobber.org", "whopper.nu" and "lala.com".
Then say this machine's name is "mail.blobber.org".
All email to lala.com is to be forwarded to whupper.nu.

.. code-block:: mysql

   INSERT INTO domains (domain) VALUES
    ('blobber.org'),
    ('mail.blobber.org'),
    ('whopper.nu'),
    ('lala.com');

   INSERT INTO aliases (mail,destination) VALUES
    ('@lala.com','@whupper.nu'),
    ('@mail.blobber.org','@blobber.org'),
    ('postmaster@whopper.nu','postmaster@localhost'),
    ('abuse@whopper.nu','abuse@localhost'),
    ('postmaster@blobber.org','postmaster@localhost'),
    ('abuse@blobber.org','abuse@localhost');
    
You also have two users called "Xandros" and "Vivita".

.. code-block:: mysql

   INSERT INTO users (id,name,maildir,crypt) VALUES 
    ('xandros@blobber.org','xandros','xandros/',encrypt('apassword') ),
    ('vivita@blobber.org','vivita','vivita/', encrypt('anotherpassword') );

   INSERT INTO aliases (mail,destination) VALUES
    ('xandros@blobber.org','xandros@blobber.org'),
    ('vivita@blobber.org','vivita@blobber.org');
    
You want all mail for whooper.nu to go to xandros (catchall).

.. code-block:: mysql

   INSERT INTO aliases (mail,destination) VALUES
        ('@whopper.nu','xandros@blobber.org');

There is also a "Karl" user, but he does want all mail forwarded to an external account.

.. code-block:: mysql

   INSERT INTO aliases (mail,destination) VALUES
    ('karl@blobber.org','karl.vovianda@gmail.com');

So what does each of these lines actually do? Well the domains are pretty straight forward.

The users are as well, it requires four fields. ID is the email address of the user, and also its username when loggin in, described later on. NAME is optional description of the user. MAILDIR is the name of the folder inside /var/spool/mail/virtual. It must end in a /, otherwise it wont be used as a unix maildir format. CRYPT is the encrypted text password to use.

The alises are the interesting part. Lets start from a top down view to see how emails get delivered:
Say an email arrives addressed to "john@whopper.nu".

Postfix looks up domains and say whopper.nu is an domain it listens to.

Postfix then looks up aliases and searches for a row where the mail field matches "john@whopper.nu".

None does so it next searches for "@whopper.nu", which is the way to specify catch all others for that domain.

It finds one row and its destination is "xandros@blobber.org".

It then searches for "xandros@blobber.org" and finds one, which destination is the same as the mail, therefore it is the final destination.

It then tries to deliver this mail. The look up says blobber.org is a local mail so it looks up users for a matching id and delivers it to its maildir.

Lets try "julian.whippit@lala.com".

Postfix looks up domains and it is an domain it listens to.

First lookup does not find this user, but the next finds the catchall "@lala.com". But its destination is another catchall, "@blobber.org".

This means Postfix will look for "julian.whippit@blobber.org". This address is not found either, nor is a catchall for blobber.org. Therefore this address is not valid and the message will be bounced.

Any mail arriving for "karl@blobber.org" or "karl@lala.com", gets forward to an external address of "karl.vovianda@gmail.com". So forwarding is simple. I tend to use a subdomain for all my friends addresses as easily I forget what their real addresses are, and I use different email clients all the time.

I also added the required aliases of postmaster and abuse to blobber.org and whopper.nu. The catchall for lala.com means they are not required for that domain.

Another useful alias to add is root, as often you get admin mail from e.g cron jobs within those domains etc. Other often used aliases are info, sysadmin, support, sales, webmaster, mail, contact and all. But they are also honeypots for spam, so just include the ones you think you will need.

添加模板
^^^^^^^^^^^^^^^^^^^^

So to add a new domain to the system, You do this, replacing the italics with relevant data:

.. code-block:: mysql

   INSERT INTO domains (domain) VALUES ('domain.tld');
   INSERT INTO aliases (mail,destination) VALUES
    ('@domain.tld','email@address'),
    ('postmaster@domain.tld','email@address'),
    ('abuse@domain.tld','email@address');

And to add a new user to the system, do this:

.. code-block:: mysql

   INSERT INTO users (id,name,maildir,crypt) VALUES
    ('email@address','short description','foldername/',encrypt('password') );
   INSERT INTO aliases (mail,destination) VALUES
    ('email@address','email@address');

通用SQL
---------

A selection of useful sql statements, if you are not using an admin/manager program to maintain your email domains and users.

Find domains without a catchall

.. code-block:: mysql
   #Remember some might be disabled
   SELECT dom.domain 
   FROM domains dom
   LEFT JOIN aliases al
       ON CONCAT( '@', dom.domain ) = al.mail
   WHERE al.mail is null
   OR al.enabled = 0
   ORDER BY dom.domain ASC
   
Find aliases for an invalid domain

.. code-block:: mysql

   SELECT al.*
   FROM aliases al
   LEFT JOIN domains dom
       ON dom.domain = SUBSTRING(al.mail,LOCATE('@',al.mail)+1)
   WHERE dom.domain is null
   OR dom.enabled = 0
   ORDER BY al.mail ASC

查找所有non local destination aliases

.. code-block:: mysql

   SELECT al.*
   FROM aliases al
   LEFT JOIN domains dom
       ON dom.domain = SUBSTRING(al.destination,LOCATE('@',al.destination)+1)
   WHERE dom.domain is null
   ORDER BY al.enabled, al.destination ASC, al.mail ASC

查找所有aliases for a certain domain

.. code-block:: mysql

   SELECT al.*
   FROM aliases al
   WHERE SUBSTRING(al.mail,LOCATE('@',al.mail)+1) = 'domain.tld'
   ORDER BY al.enabled, al.mail ASC

查找所有aliases for a certain domains, checking if enabled for both domain and alias

.. code-block:: mysql

   select * 
   from domains d
   join aliases a
     on a.mail like concat( '%','@',d.domain)
     and a.enabled = 1
   where d.enabled = 1
   and d.domain like '%foobar%'
   order by d.domain,a.mail
