外部修改
================

Before making any changes you need to have done a few steps externally. (Or at least before you start testing).

域名
--------------

You need a domain name to use with your email server. This may be one you purchased, or a subdomain of an existing one, or a dynamic one e.g. dyndns.org.

DNS
-----------

You will also need to configure the MX details for the DNS of this server. This is done via your domain registrar, or sometimes an external nameserver(DNS) provider. You can also host your own DNS via packages such as Bind.

Your provider might let you do this through a GUI, but in this is technically how a the configuration should look like::

   domain.tld    IN   MX   10   yourmailserver.domain.tld

(Replace domain.tld with your domain name, and yourmailserver.domain.tld with the full name of your mail server). Repeat this for each domain that you want the server to handle.

Further mx entries are possible in the same file, if there are subdomains. And also if you have backup MX servers. Refer to my backup MX section if interested.

.. Note:: Some other mail systems will check via reverse DNS for a match between IP and mail server name, as part of their spam scoring.

If people need suggestions for domain registrars or dns providers then let me know

You know have a finished mail server. This is as far as the main guide goes. Hope it was clear enough to follow.

Now it is time to insert data, and to test how it works.

Feel free to extend it with my suggestions further down.

Ive created an EC2 bundle for this stage: flurdy-amis/ubuntu-mail-server-webmail.

