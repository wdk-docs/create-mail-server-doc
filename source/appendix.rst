附录
=============

关于作者
---------

Ivar Abrahamsen, an IT Senior Consultant from Norway. Specialising in developing and integrating middleware application systems. Mainly open source and Java based technology stack. Recently moved back to UK (Hampshire) after 5 years in Oslo and before that 15 years in Manchester.

联系
------

Remember I have stood on the shoulders of giants. I just ended up with a system that worked for me, and decided to document its evolution.

检测列表
-----------
Before contacting, have you?:
* Read this document properly? Followed it step by step?
   (While we can not insist on the same setup for everyone, assistance is easier and more likely if less customised)
* Have tested properly? Applied the solutions provided in the test section?
* Read the forums for solutions already posted?
* Read the FAQ?
* Tailed the mail.log? It usually tells you what the problem is!
* Tailed the mysql.log? If nothing happens there it should indicate something...
* Include a short dump of the mail.log with your post. (Remember to anonymise the servernames, email adresses & definetly passwords!)

论坛
------

Use the Ubuntu forums! :)

* Here is a thread on this specific mail server howto.
* And another one by me which is also used..
* Or alternatiely use Server Fault.

Please participate in the forums.

* If you see an issue you also have, contribute with more information.
* And even better if it something you may know how to solve, please let people know.
* And especially, if you post a problem, then solve it, let people know what the solution was! (and not just that you solved it...)

I am rubbish in replying to emails, and the forums are read and answered by people whom know a lot more about Postfix than me.
Questions sent to me directly may not be answered for a while or at all unfortunetly.

咨询和建议
-----------------------

Not to be rude, but in attempt to reduce the volume of emails I receive please consider the following:

* If you simply want advice please use the forum or Server Fault.
* My references and the people whom follow the forums and the postfix mailling lists are much more knowledgable than me.
* People whom follow the forums and the postfix mailling lists are much more likely to reply than me.
* I am a firm believer in: Give a man a fish; you have fed him for today. Teach a man to fish; and you have fed him for a lifetime. (Playing far too much Civ was not all wasted..) 
  Or rather my version: "Teach a man to fish, share the teaching, and you have fed many others for a lifetime".
* So if you have any questions, problems with using this guide, or any other issues with this guide, please use the forum.
   Then it is also an available archive for others to find solutions in the future.
* Any technical difference of opinion, please use the forum.
* Interested in Postfix, or got a technical query about it? Considered the postfix mailling lists.
* If you made / found an extension / alternative / translation to this tutorial, fantastic! Please let me know, and I'll link to it. And shout at me, if I am slow in doing so. :)
* Any clear technical mistakes by me in this guide, then let me know, but perhaps discuss it first in the forums?
* If you find any spelling mistakes or broken links, please let me know. 
   Even after 6-7 years since the first version people are still finding typos every week. 
   So my initial versions must have contained only typos! :0
* Thank you messages are very appreciated however! Actually it makes my day. :)
(People whom buy a T-shirt of me makes my week.. ;p )

If you still want my help:

* If you still need advice you may hire me as a consultant via www.eray.co.uk.
* You may hire me to set up a complete email server for you via www.eray.co.uk.
* However if you:

   * are an individual or non commercial organisation
   * have tried all the steps mentioned above: test section, FAQ, forums, mailing lists.
      then you can contact me via flurdy.com/contact

10 years after the initial version of this document I think roughly 95% of the people that do contact me directly, have not bothered to test properly (command line first, not straight to GUI...) nor read the whole guide and common solutions & forums.

They just want me do to their work for them. For free.

The chances of me replying to their emails are miniscule, I much prefer to spend time with my daughter instead :)

However many people contribute with really good stuff in the forums, send me typos(frequently..), or thank you notes. You guys are great!

为什么
----------

**Why your own mail server**

Main reason: Because you can.

Other good reasons: Basically it leaves you in complete control, to expand, customize and tweak your mail server to your needs. You are not dependant on 3rd party providers, limited by their technology contraints or your budgets. With your own mail server you can add as many aliases, users and domain as you'd like, be as restrictive or open about security, virus, spam, file sizes etc as you prefer. And is it is well known, frequently updated, open source application stack, you can also trust the software you use.

**Why I wrote this howto**

When I set up my first email server I used a mix of other howtos on the net. And they were so helpfull that I though I would contribute back with my experience. And it has been useful as a recipe script for myself every time I need to install/update a server.

A less angelic reason is that back in 2003 I was setting up mail server for a few friends and collegues. Soon I was getting more request, and being a lazy programmer, I thought.. "Why don't I write a howto and let them do it themselves..." Soon it was listed on postfix.org and I was getting thousends of hits and lots of emails. (blessing in disguise)

参考
--------------

* Postfix howtos
* Kyle's book
* John Locke on TechRepublic
* Hildebrandt's book
* Hildebrandt's website
* List-Petersen
* Genco Yilmaz
* Christop Haas
* Nenzel & Peet
* Peters
* Matthews
* Stepanov
* Andy "Besy"
* Meta Consultancy

新参考

* Postfix TLS
* Postfix main.cf doc
* saslauthd
* Bypassing amavisd
* Ubuntu Help: Squirrelmail

软件链接
---------------

Please refer to the previous edition for a list of urls and suiteable downloads. However most are unneccessary with decent package manager.

Difference between Ubuntu versions
--------------------------------------

I used to distinguish differences, which were available in a previous edition.

In the last few editions I have not. I base it on the latest LTS version, and test it with the minor releases in between.

下载
---------------------

Please refer to the previous edition for a complete lists of downloads that are available.
However all the required software are available as .deb packages direct from ubuntu repositories.

修改日志
--------------

Brief list of latest changes.

* 2013-01-10: Made Roundcube default webmail client.
* 2011-11-18: Added fail2ban.
* 2011-10-10: (Re)Added domain and DNS section
* 2010-06-09: Improved Google Apps integration. Added backup relay recipient lookup. Update phpmyadmin section.
* 2010-06-07: Updated for Ubuntu 10.04 LTS Lucid Lynx Modified mysql log option. Removed dynamic uid & gid in postfix.
* 2010-02-15: Redid SASL secure authentication section.
* 2009-12-16: Expanded test section with text from older editions and new babble.
* 2009-11-25: Bumped to edition 9!
   And added Roundcube as webmail client.
* 2009-11-11: Updated to work with 9.10 Karmic Koala.
* 2009-06-04: made basic server image available on ec2. based canonical's official ec2 ami.
* 2009-06-02: made clean server image available on ec2. based canonical's official ec2 ami.
* 2009-05-29: changed contact section.
* 2009-05-29: started 8th edition
   Used to refer to all changes, but got too long. A previous edition contains such a list.

Todo
-------

* Spell check!
* Remove uid and guid

Please refer to the previous edition for some old todos....

FAQ
-----

There is not yet an extensive FAQ.

But please, most of the frequent questions have been asked and answered in the forums.

Most are also unneccessary as following the test section will have solved them.

Some question that frequently get sent to me, which first of all should have been asked in the forums and has been answered there many times, which then I tend to ignore are:

* Squirrelmail does not allow me to log in
   This is due to many things. Most are due to skipping too fast forward, ignoring test sections etc.
   Answers:

   * Does postfix work?
      No point trying to run before you can crawl. Send emails to recipients on your server, tail mail.log to see if everything is okay.
      Often mysql is not configured properly, check the mysql logs for activity.
   * Have they ever received an email?
      If not they can not log into squirrelmail as the email folders will not yet exist.
   * Does Courier work?
      If it doesn't then you have still got some more setup to do.
   * If all above is okay, then it may be a problem with your Squirrelmail setup.
      Check empty spaces in squirrelmail mysql setup. More details in test section.
* Email folders do not exist
   Mentioned many times in this guide and forums.
   Answers:

   * Have they received an email?
     If not they you can not log into squirrelmail as the email folders will not yet exist. When receiving their first email, postfix will create all the neccessary folders. If it does not your postfix setup is broken.
   * There is a program that creates the folders for you.
     I do not recommend it, as basically your postfix setup is broken if no folders are created, and you better fix it instead.
* SASL authentication does not work
   All lot of people have issues with SASL in certain setups. There are quite a few messages in the forum is regarding this.
   SASL works for me, but I can not tell my configuration apart from other people's server where it does not.
   Workarounds or alternatives:
   
   * Do you need external SMTP & IMAP access? Or will webmail interface be sufficient for external dynamic ip clients? Then you do not need SASL. Restrict by IP and TLS is enough.
   * Do you need SASL at all? If you accept TLS only and restrict by IP addresses in mynetworks it may not be neccessary. Not an option with road warriors and too random dynamic IPs unfortunately.
   * Could POP-before-SMTP be an alternative?
   * There are a few suggestions in the forum.
     Note however most people assume SASL is the problem when another factor that is the root cause.
* (!!)file(1) utility (/usr/bin/file) FAILED: run_command: can't fork: Cannot allocate memory at /usr/sbin/amavisd-new line 3077.
   Running postfix on a micro instance on Amazon ec2?
   It only has 600Mb of ram and amavisd+clamav can grow too big over time. Restart these services, and even some times reboot...
