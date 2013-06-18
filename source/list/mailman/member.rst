用户手册
==============

介绍
----------------

This document is intended to help the members of a Mailman 2.1 mailing list learn to use the features available to them. It covers the use of the web and email interfaces for subscribing and unsubscribing, changing member options, getting password reminders and other subscriber-level tasks. It also answers some common questions of interest to Mailman list members.

Information for list and site administrators is provided in other documents.

This document need not be read in order. If you are simply looking for an answer to a specific question, jump to the appropriate place and references to other sections will be provided if necessary or potentially helpful.

	Note
For the purposes of this document, we assume that the reader is familiar with common terms related to email (eg: Subject line, body of the message) and web sites (eg: drop-down box, button) or can look them up. We also assume that the reader can already use his or her email program and web browser well enough that instructions such as "send email to this address" or "visit this web page" or "fill in the form provided" are clear. If you are not familiar with these actions, you may want to consult other documentation to learn how to do these things with your particular setup.

致谢
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sections of this document have been borrowed from the List Administrator Manual found in Mailman CVS, which was written by Barry A. Warsaw, and from the in-line help for Mailman 2.1.

The rest of this manual has been written by Terri Oda, and (hopefully soon) edited and added to by members of the mailman community via the mailman wiki at http://wiki.list.org. Please see the wiki changelogs for more information.

Terri has been maintaining mailing lists since the year she attained voting age in Canada, although the two are not related. She currently oversees the mailing lists at Linuxchix.org, as well as several smaller servers. In the world outside of list administration, Terri is doing work with an artificial life spam detector, and is actually more of a programmer than technical writer.

Proofreading thanks go to Margaret McCarthy and Jason Walton.

许可
^^^^^^^^^^^^^^^^^^

Please feel free to use this document in any way that will help you with your installation of Mailman. You are expressly permitted to distribute copies to your users and make modified copies that reflect your specific setup (for example, replacing all the instances of WEBSERVER and DOMAIN with the appropriate actual values). You can also get printable versions of this document in HTML or printable versions of this document in PDF (currently requires a wiki login, which you can create for free).

It is highly recommended that you provide a link to the latest version of this document, which can be found at

http://wiki.list.org/display/DOC/Mailman+2.1+Members+Manual

And if you happen to fix any typos or add sections that may be of interest to all Mailman users, please consider contributing them back to the community by editing the wiki version of this document.

什么是邮件列表?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A mailing list is simply a list of addresses to which the same information is being sent. If you were a magazine publisher, you would have a list of the mailing addresses of all the subscribers to the magazine. In the case of an electronic mailing list, we use a list of email addresses from people interested in hearing about or discussing a given topic.

Two common types of email mailing lists are announcement lists and discussion lists.

Announcement lists are are used so that one person or group can send announcements to a group of people, much like a magazine publisher's mailing list is used to send out magazines. For example, a band may use a mailing list to let their fan base know about their upcoming concerts.

A discussion list is used to allow a group of people to discuss topics amongst themselves, with everyone able to send mail to the list and have it distributed to everyone in the group. This discussion may also be moderated, so only selected posts are sent on to the group as a whole, or only certain people are allowed to send to the group. For example, a group of model plane enthusiasts might use a mailing list to share tips about model construction and flying.

Some common terms:

A "post" typically denotes a message sent to a mailing list. (Think of posting a message on a bulletin board.)
People who are part of an electronic mailing list are usually called the list's "members" or "subscribers."
"List administrators" are the people in charge of maintaining that one list. Lists may have one or more administrators.
A list may also have people in charge of reading posts and deciding if they should be sent on to all subscribers. These people are called list moderators.
Often more than one electronic mailing list will be run using the same piece of software. The person who maintains the software which runs the lists is called the "site administrator." Often the site administrator also administrates individual lists.

GNU Mailman
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

GNU Mailman is software that lets you manage electronic mailing lists. It supports a wide range of mailing list types, such as general discussion lists and announce-only lists. Mailman has extensive features which make it good for list subscribers, such as easy subscription and unsubscription,
privacy options, and the ability to temporarily stop getting posts from the list. The list member features are covered in this document.

Mailman also has many features which make it attractive to list and site administrators. These features are covered in the list and site administrator manuals.

翻译从我们的例子中到真正的清单
------------------------------------------------------

Often, it's easier to simply give an example than explain exactly how to find the address for your specific list. As such, we'll frequently give examples for a fictional list called LISTNAME@DOMAIN whose list information page can be found at http://WEBSERVER/mailman/listinfo/LISTNAME.

Neither of these are real addresses, but they show the form of a typical list address. The capital letters used for the list-specific parts of each address should make it easier to see what should be changed for each list. Although specific list configurations may be different, you will probably be able to just replace the words given in capital letters with the appropriate values for a real list:

LISTNAME
The name of your list.
DOMAIN
The name of the mail server which handles that list.
WEBSERVER
The name of the web server which handles the list web interface. This may be the same as DOMAIN, and often refers to the same machine, but does not have to be identical.
As a real-life example, if you are interested in the mailman-users list, you'd make the following substitutions: LISTNAME=mailman-users, DOMAIN=python.org, WEBSERVER=mail.python.org. As such, for the mailman-users@python.org mailing list, the list information page can be found at the URL
http://mail.python.org/mailman/listinfo/mailman-users. (These, unlike most of the examples given in this document, are real addresses.)

Most lists will have this information stored in the List-* headers. Many mail programs will hide these by default, so you may have to choose to view full headers before you can see these informational headers.

Mailman的界面
------------------------------------------------------

Mailman has two different interfaces for the list subscriber: the web interface and the email interface. Most discussion list subscribers use the email interface, since this includes the email address you use to send mail to all the subscribers of that list.

The interface you use for changing options is largely a matter of preference, since most (but not all) of the options which can be changed from the web interface can also be changed by email. Usually it is easier to use the web interface for changing options, since the web interface provides instructions as you go, but there are times when people may prefer the email interface, so both are provided.

Web界面
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The web interface of Mailman is its selling point for many administrators, since it makes it much easier for subscribers and administrators to see which options are available, and what these options do.

Every mailing list is also accessible by a number of web pages. Note that the exact URLs are configurable by the site administrator, so they may be different than what's described below. We'll describe the most common configuration, but check with your site administrator or hosting
service for details.

List information (listinfo) page

Usually found at http://WEBSERVER/mailman/listinfo/LISTNAME (for example, http://lists.example.com/mailman/listinfo/mylist)
The listinfo page is the starting point for the subscriber interface. As one would assume from the name it's given, it contains information about the LISTNAME list. Usually all the other subscriber pages can be accessed from this point, so you really only need to know this one address.
Member options page

Usually found at http://WEBSERVER/mailman/options/LISTNAME/EMAIL (For example, http://lists.example.com/mailman/options/mylist/kathy@here.com)
This page can also be accessed by going to the listinfo page and entering your email address into the box beside the button marked "Unsubscribe or Edit Options" (this is near the bottom of the page).
The member options page allows you to log in/out and change your list settings, as well as unsubscribe or get a copy of your password mailed to you.
To log in to your member options page: If you are not already logged in, there will be a box near the top for you to enter your password. (If you do not know your password, see Section 6.1: How do I get my password? for more information on getting your password.) Enter your password in the box and press the button.
Once you are logged in, you will be able to view and change all your list settings.
List Archives

Usually found at http://WEBSERVER/pipermail/LISTNAME if the list is publicly archived, and http://WEBSERVER/mailman/private/LISTNAME if the list is privately archives. (For example, http://lists.example.com/pipermail/mylist or http://lists.example.com/mailman/private/mylist)
The list archive pages have copies of the posts sent to the mailing list, usually grouped by month. In each monthly group, the posts are usually indexed by author, date, thread, and subject.
Note: Pipermail is the name of the default archiver that comes with Mailman. Other archive programs are available.
If the archive is private, you will need to supply your subscribed email address and your password to log in. (See Section 6.1: How do I get my password? for more information on getting your password.)

email界面
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Every mailing list has a set of email addresses to which messages can be sent. There's always one address for posting messages to the list, one address to which bounces are sent, and addresses for processing email commands. For a fictional mailing list called mylist@example.com, you'd find these addresses:

mylist@example.com
this is the email address people should use for new postings to the list.
mylist-join@example.com
by sending a message to this address, a new member can request subscription to the list. Both the Subject: header and body of such a message are ignored. Note that mylist-subscribe@example.com is an alias for the -join address.
mylist-leave@example.com
by sending a message to this address, a member can request unsubscription from the list. As with the -join address, the Subject: header and body of the message is ignored. Note that mylist-unsubscribe@example.com is an alias for the -leave address.
mylist-owner@example.com
This address reaches the list owner and list moderators directly. This is the address you use if you need to contact the person or people in charge.
mylist-request@example.com
This address reaches a mail robot which processes email commands that can be used to set member subscription options, as well as process other commands. A list of members' email commands is provided in Appendix A.
mylist-bounces@example.com
This address receives bounces from members whose addresses have become either temporarily or permanently inactive. The -bounces address is also a mail robot that processes bounces and automatically disables or removes members as configured in the bounce processing settings. Any bounce messages that are either unrecognized, or do not seem to contain member addresses, are forwarded to the list administrators.
mylist-confirm@example.com
This address is another email robot, which processes confirmation messages for subscription and unsubscription requests.
There's also an -admin address which also reaches the list administrators, but this address only exists for compatibility with older versions of Mailman.

For changing options, we use the LISTNAME-request address (for example, mylist-request@example.com).

Commands can appear in the subject line or the body of the message. Each command should be on a separate line. If your mail program automatically appends a signature to your messages, you may want to put the word "end" (without the quotes) on a separate line after your other commands. The end command tells Mailman not to process the email after that point.

The most important command is probably the "help" command, since it makes Mailman return a message full of useful information about the email commands and directions to the web interface.

Quick references to the subscriber commands have been provided in Appendices A and B. (These have been slightly adapted from the output of the help command.)

我需要人类说话!
-------------------------------

If you have any trouble with any of these commands, you can always reach the person or people in charge of a list by using the list administrator email address. The list administrators can help you figure out how to do something, subscribe/unsubscribe you, or change your settings if you are unable to change them yourself for some reason. Please remember that many mailing list administrators are volunteers who are donating their spare time to run the list, and they may be very busy people.

This list administrator email address is in the form LISTNAME-owner@DOMAIN, where LISTNAME is the name of the list (eg: mailman-users) and DOMAIN is the name of the server (eg: python.org). This email address, along with the email addresses of specific administrators, is given on the bottom of the list information pages. See Section 3.1: The web interface for more information on finding the list information page for your list

订阅和退订
-----------------------------------

Since subscribing (joining) and unsubscribing (leaving) lists are often the only things a list member needs to know, these can both be done without requiring you to know a password.

如何加入列表? (订阅)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

There are two common ways you can subscribe to a Mailman mailing list.

Using the web interface:

1. Go to the list information page for the list you want to join. (This will probably be something like http://WEBSERVER/mailman/listinfo/LISTNAME)
2. Look for the section marked "Subscribing to LISTNAME" and fill in the boxes. You can fill in the following:

You must enter your email address.
You may choose to supply your real name.
You may choose a password. If you do not choose one, Mailman will generate one for you.
	
Do NOT use a valuable password, since this password may be mailed to you in plain text.
If the list supports more than one language, you may be able to choose your preferred language. Note: This setting does not affect posts to the list, only pre-prepared Mailman texts such as your member options page.
3. Press the subscribe button. A new page should appear telling you that your request has been sent.
Using the email interface:

1. Open a mail program which sends mail from the address you want to subscribe.
2. Send a mail to the list subscription address, which will be in the form LISTNAME-join@DOMAIN. The subject and body of the message will be ignored, so it doesn't matter what you put there. You may also use LISTNAME-subscribe@DOMAIN.

After following one of these sets of instructions (you don't need to do both!), there are a few possible outcomes depending upon the settings for that list.

You may receive an email message asking for confirmation that you really want to be subscribed to the list. This is to prevent anyone from subscribing you to lists without your permission. Follow the instructions given in the message to confirm your wish to be subscribed.
A moderator may also need to confirm your subscription if you are subscribing to a limited list.
Or you may have to wait for a moderator and follow the instructions in the confirmation mail.
Once this is done, you will likely receive another message welcoming you to the list. This message contains some useful information including your list password and some quick links for changing your options, so you may want to save it for later reference.

Note: Subscribing can be done in other ways as well. See Appendix A for more advanced email subscribing commands.

如何离开列表? (退订)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Don't want to be on a list any more? If you're just going on vacation or are too busy to read mails and want to temporarily turn them off, you may want to stop mail delivery rather than unsubscribing. This means you keep your password and other settings so you can, for example, still have access to private list archives. If this is what you'd prefer, see Section 7.1 for instructions on disabling mail delivery temporarily.

If you actually want to leave the list, there are two common ways you can unsubscribe from a Mailman mailing list.

Using the web interface:

1. Go to the list information page for the list you want to leave. (This will probably be something like http://WEBSERVER/mailman/listinfo/LISTNAME)
2. Look for the section marked "LISTNAME subscribers" (usually found near the bottom of the page).
3. There should be a button marked "Unsubscribe or Edit Options." Enter your email address in the box beside this button and press the button.
4. You should be brought to a new page which has an "Unsubscribe" button. Press it to unsubscribe and follow the instructions given.

Using the email interface:

1. Open a mail program which sends mail from the address you want to unsubscribe.
2. Send a mail to the list unsubscribe address, which will be of the form LISTNAME-leave@DOMAIN. The subject and body of this message will be ignored, so it doesn't matter what you put there. You may also use LISTNAME-unsubscribe@DOMAIN.

After following one of these sets of instructions (you don't need to do both!), you will be sent a confirmation mail and must follow the instructions given in that mail to complete the unsubscription. This is to stop people from unsubscribing you without your permission. In addition, a moderator may need to approve your unsubscription.

If you do not receive this confirmation mail with instructions, make sure that you typed your email address correctly (if you were using the web interface to unsubscribe) and that the address you tried to unsubscribe is, indeed, actually subscribed to that list. For security reasons, Mailman generates the same member options page regardless of whether the address entered is subscribed or not. This means that people cannot use this part of the web interface to find out if someone is subscribed to the list, but it also means that it's hard to tell if you just made a typo.

Once your unsubscription has been processed, you will will probably receive another message confirming your unsubscription from the list, and at that point you should stop receiving messages.

If you wish to skip the confirmation process (for example, you might be unsubscribing an address which no longer works), it is possible to bypass it by using your password instead and either logging in to your options page using it (See Section 3.1), or sending it with your email commands to LISTNAME-request (See Appendix A for advanced email unsubscription commands). See Section 6.1 for more information on getting your password.

密码
-----------------------

Your password was either set by you or generated by Mailman when you subscribed. You probably got a copy of it in a welcome message sent when you joined the list, and you may also receive a reminder of it every month. It is used to verify your identity to Mailman so that only the holder of the password (you!) and the administrators can view and change your settings.

	
Do NOT use a valuable password for Mailman, since it can be sent in plain text to you.

获取密码
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you've forgotten your password and haven't saved the welcome message or any reminder messages, you can always get a reminder through the web interface:

Go to the list information page for the list from which you wish to get your password (This will probably be something like http://WEBSERVER/mailman/listinfo/LISTNAME)
Look for the section marked "LISTNAME subscribers" (this section is usually found near the bottom of the page).
There should be a button marked "Unsubscribe or Edit Options." Enter your email address in the box beside this button and press the button.
You should be brought to a new page which has an "Password Reminder" section. Press the "Remind" button to have your password emailed to you.
If you do not receive the password reminder email after doing this, make sure that you typed your email address correctly and that the address you used is, indeed, actually subscribed to that list. For security reasons, Mailman generates the same member options page regardless of whether the address entered is subscribed or not. This means that people cannot use this part of the web interface to find out if someone is subscribed to the list, but it also means that it's hard to tell if you just made a typo.

You can also get a reminder using the email interface,

Send a mail to LISTNAME-request@DOMAIN with the command password
Commands can appear in either the body or the subject of the message. (See Section 3.2 for more information about sending mail commands.)

If you are not sending mail from your subscribed address, you can also specify this address by sending the command password address=$<$ADDRESS$>$.

修改密码
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Do NOT use a valuable password, since this password may be mailed to you in plain text.
From the web interface:

Log in to your member options page. (See Section 3.1 for instructions on how to do this.)
Look for the password changing boxes on the right-hand side of the page and enter your new password in the appropriate boxes, then press the button marked "Change My Password."
This can also be changed for multiple lists at the same time if you are subscribed to more than one list on the same domain. See Section 10.1 for information about changing settings globally.

From the email interface:

Send a mail to LISTNAME-request@DOMAIN with the command password <OLDPASSWORD> <NEWPASSWORD>.
Commands can appear in either the body or the subject of the message. (See Section 3.2 for more information about sending mail commands.)

If you are not sending mail from your membership address, you can also specify this address with address=<ADDRESS> after <NEWPASSWORD>.

For example, if kathy@here.com wanted to change her mylist password from zirc to miko, but she was sending mail from her work address kathy@work.com, she could send a message to mylist-request@example.com with the subject set to password zirc miko address=kathy@here.com.

开启/关闭记住密码(记住选项)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you don't wish to the reminder email including your password every month, you can disable it from the member options page. (You can always get the password mailed out when you actually want it. See Section 6.1 for instructions.)

Using the web interface:

Log in to your member options page. (See Section 3.1 for instructions on how to do this.)
Look for the section marked "Get password reminder email for this list?" and change the value accordingly.
This can also be changed for multiple lists at the same time if you are subscribed to more than one list on the same domain. See Section 10.1 for information about changing settings globally.

Using the email interface:

Send a mail to LISTNAME-request@DOMAIN with the command set reminders on or set reminders off.
Commands can appear in either the body or the subject of the message. (See Section 3.2 for more information about sending mail commands.)
Set it to "on" to receive reminders, and "off" to stop receiving reminders.

变更邮件传送
--------------------------------

开启/关闭邮件传输? (传输选项)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You may wish to temporarily stop getting messages from the list without having to unsubscribe. If you disable mail delivery, you will no longer receive messages, but will still be a subscriber and will retain your password and other settings.

This can be handy in a many different cases. For example, you could be going on vacation or need a break from the list because you're too busy to read any extra mail. Many mailing lists also allow only subscribers to post to the list, so if you commonly send mail from more than one address (eg, one address for at home and another for when you're travelling), you may want to have more than one subscribed account, but have only one of them actually receive mail. You can also use this as a way to read private archives even on a list which may be too busy for you to have sent directly to your mailbox. All you need to do is subscribe, disable mail delivery, and use your password and email to log in to the archives.

To disable/enable mail delivery using the web interface:

Log in to your options page. (See Section 3.1 for instructions.)
Go down to the section marked "Mail delivery" and select "Disabled" to stop receiving mail, and "Enabled" to start receiving mail.
This can also be changed for multiple lists at the same time if you are subscribed to more than one list on the same domain. See Section 10.1 for information about changing settings globally.

To disable/enable mail delivery using the email interface:

Send a mail to LISTNAME-request@DOMAIN with the command set delivery off or set delivery on.
Commands can appear in either the body or the subject of the message. (See Section 3.2 for more information about sending mail commands.)
Set it to "off" to stop receiving posts, and "on" to start receiving them again.

避免重复消息 (重复选项)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Mailman can't completely stop you from getting duplicate messages, but it can help. One common reason people get multiple copies of a mail is that the sender has used a "group reply" function to send mail to both the list and some number of individuals. If you want to avoid getting these messages, Mailman can be set to check and see if you are in the To: or CC: lines of the message. If your address appears there, then Mailman can be told not to deliver another copy to you.

To turn this on or off using the web interface:

Log in to your member options page. (See Section 3.1 for more details on how to do this.)
Scroll down to the bottom of the page to the section marked "Avoid duplicate copies of messages?" and change the value accordingly.
This can also be changed for multiple lists at the same time if you are subscribed to more than one list on the same domain. See Section 10.1 for information about changing settings globally.

To turn this on or off using the email interface:

Send a mail to LISTNAME-request@DOMAIN with the command set duplicates on or set duplicates off.
Commands can appear in either the body or the subject of the message. (See Section 3.2 for more information about sending mail commands.)
Set it to "on" to receive list copies of messages already sent to you, set it to "off" to avoid receiving these duplicates.

修改订阅地址?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To change your subscription address,

Log in to your member options page. (See Section 3.1 for more details on how to do this.)
In the section marked "Changing your LISTNAME membership information," enter your new address.
If you wish to change your address for all subscriptions using the old address, select the "Change globally" box. If you have subscriptions under another address or for lists on a different domain, these will have to be done separately. See Section 10.1 for more information about changing settings globally.
There is no special way to do this from the email interface, but you can subscribe and unsubscribe for more or less the same effect. (See Sections 5.1 and 5.2 for more information on subscribing and unsubscribing.)

启动/停止获取我的帖子拷贝 (myposts option)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

By default in Mailman, you get a copy of every post you send to the list. Some people like this since it lets them know when the post has gone through and means they have a copy of their own words with the rest of a discussion, but others don't want to bother downloading copies of their own posts.

	
This option has no effect if you are receiving digests.
You may also want to see Section 7.5, which discusses acknowledgement emails for posts sent to the list.

To set this using the web interface:

Log in to your member options page. (See Section 3.1 for more details on how to do this.)
Look for the section marked "Receive your own posts to the list?" Set it to "Yes" to receive copies of your own posts, and "No" to avoid receiving them.
To set this using the email interface:

Send a mail to LISTNAME-request@DOMAIN with the command set myposts on or set myposts off.
Commands can appear in either the body or the subject of the message. (See Section 3.2 for more information about sending mail commands.)
Set it to "on" to receive copies of your own posts, and "off" to avoid receiving them.

告知列表收录帖子 (ack option)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

On most lists, you will simply receive a copy of your mail when it has gone through the list software, but if this is disabled (See Section 7.4), your list mail delivery is disabled (See Section 7.1), you are not subscribed to that topic (See Section 9.2) or you simply want an extra acknowledgement from the system, this option may be useful to you.

Note: If you are not subscribed to the list, this option cannot be used. You must either check the archives yourself (if the list has public archives), ask someone who is subscribed to the list, or subscribe to use this option.

To set this using the web interface:

Log in to your member options page. (See Section 3.1 for more details on how to do this.)
Look for the section marked "Receive acknowledgement mail when you send mail to the list?" Set it to "Yes" to receive a mail letting you know your post has been received, and "No" to avoid receiving such an acknowledgement.
To set this using the email interface:

Send a mail to LISTNAME-request@DOMAIN with the command set ack on or set ack off.
Commands can appear in either the body or the subject of the message. (See Section 3.2 for more information about sending mail commands.)
Set it to "on" if you wish to receive mail letting you know your post has been received, and "off" to avoid receiving such an acknowledgement.

没有从列表获取邮件
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

There are a few common reasons for this:

No one has sent any mail to the list(s) you're on for a little while.
To check if this is the case, try visiting the archives of the list (assuming that the list has archives). If the list has no archives, you may have to ask another subscriber. (See Section 3.1 for help in finding the list archives.)

Note: Generally, it is considered impolite to send test messages to the entire list. If you feel a need to test that the list is working and for some reason you cannot simply compose a regular message to the list, it is less disruptive to send a help message to the list request address (LISTNAME-request@DOMAIN) to see if that works, or to contact the list administrator (LISTNAME-owner@DOMAIN) to ask if the list is working.

You were bouncing mail and have had mail delivery (temporarily) disabled by the list software.
If your mail provider "bounces" too many messages (that is, it tells Mailman that the message could not be delivered) Mailman eventually stops trying to send you mail. This feature allows Mailman to gracefully handle addresses which no longer exist (for example, the subscriber has found a new internet service provider and forgot to unsubscribe the old address), as well as addresses which are temporarily out-of-service (for example, the subscriber has used up all of the allotted space for his or her email account, or the subscriber's mail provider is experiencing difficulties).

Even if you are unaware of any difficulties with your mail provider, it is a good idea to check this. Some popular webmail providers and internet servers are not as reliable as one might assume, nor is the internet as a whole. You may want to also send yourself a test message from another account or ask a friend to send you a test message to make sure your subscribed address is working.

To check if this may be the reason you are not receiving messages, log in to the your options page (See Section 3.1 for more details on how to do this) and look at your options. There should be one marked "Mail Delivery" - if it is set to "Disabled," set it to "Enabled" to start receiving mail again. (For more instructions on disabling or enabling mail delivery, see Section 7.1.)

Note: Even if you have not been disabled at the time you check, you could be bouncing messages and not have reached the threshold for your subscription to be disabled. You may need to check again.

There is a delay or break in the networks between you and the list server.
No matter what many of us would like, the internet is not 100% reliable, nor is it always fast. Sometimes, messages simply take a long time to get to you. Try to be patient, especially if the server is far (in terms of networks, not geography, although often one implies the other) from your internet service provider.

To check if this might be causing your problem, you can try pinging the list server or tracing the route between you and it. (Instructions on how to do this varies from platform to platform, so you may want to use a search engine to find those appropriate for you.)

The Mailman installation on the list server is not functioning or not functioning properly.
To test if this is a case, try visiting the list's web interface and try sending a message to LISTNAME-request@DOMAIN with the command "help" (without the quotes) in the Subject:. If neither of these works after a reasonable length of time, this may be the problem. You may wish to contact either the list or site administrator(s).

精华
------------

8.1 How can I start or stop getting the list posts grouped into one big email? (digest option)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Groups of posts are called "digests" in Mailman. Rather than get messages one at a time, you can get messages grouped together. On a moderately busy list, this typically means you get one email per day, although it may be more or less frequent depending upon the list.

You may also want to look at Section 8.2 which discusses MIME and plain text digests.

To turn digest mode on or off using the web interface,

Log in to your member options page. (See Section 3.1 for more details on how to do this.)
Look for the section marked "Set Digest Mode."
Set it to "On" to receive messages bundled together in digests. Set it to "Off" to receive posts separately.

To turn digest mode on or off using the email interface,

Send a mail to LISTNAME-request@DOMAIN with the command set digest plain or set digest mime or set digest off.
Commands can appear in either the body or the subject of the message. (See Section 3.2 for more information about sending mail commands.)
Set it to "off" if you wish to receive individual posts separately, and to "plain" or "mime" to receive posts grouped into one large mail. See Section 8.2 for more information on plain versus MIME digests.

8.2 What are MIME and Plain Text Digests? How do I change which one I get? (digest option)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

MIME is short for Multipurpose Internet Mail Extensions. It is used to send things by email which are not necessarily simple plain text. (For example, MIME would be used if you were sending a picture of your dog to a friend.)

A MIME digest has each message as an attachment inside the message, along with a summary table of contents.

A plain text digest is a simpler form of digest, which should be readable even in mail readers which don't support MIME. The messages are simply put one after the other into one large text message.

Most modern mail programs do support MIME, so you only need to choose plain text digests if you are having trouble reading the MIME ones.

Note: This option has no effect if you are not receiving mail bunched as digests. (See Section 8.1 for more information on receiving mail as digests.)

To set your digest type using the web interface:

Log in to your member options page. (See Section 3.1 for more details on how to do this.)
Look for the section marked "Get MIME or Plain Text Digests?."
Set it to "MIME" to receive digests in MIME format, or "Plain text" to receive digests in plain text format.
This can also be changed for multiple lists at the same time if you are subscribed to more than one list on the same domain. See Section 10.1 for information about changing settings globally.

To set your digest type using the email interface,

Send a mail to LISTNAME-request@DOMAIN with the command set digest plain or set digest mime.
Commands can appear in either the body or the subject of the message. (See Section 3.2 for more information about sending mail commands.)
Set it to "plain" to get posts bundled into a plain text digest, or "mime" to get posts bundled together into a MIME digest.

邮件列表主题
------------------------

Some lists are set up so that different topics are handled by Mailman. For example, the courses list on Linuxchix.org is a discussion list for courses being run by linuxchix members, and often there are several courses being run at the same time. (eg: Networking for beginners, C programming, LaTeX document mark up.) Each of the courses being run is a separate topic on the list so that people can choose only to receive the course they want to take.

These topics must be configured by the list administrator, but it is the responsibility of each poster to make sure that their post is put with the correct topic. Usually, this means adding a tag of some type to the subject line (eg: [Networking] What type of cables do I need?) or making sure the Keywords: line has the right information. (By default, you can put a Keywords: section in the beginning of the body of your message, but this can be configured by your list administrator.) Note that these tags are case-insensitive.

9.1 How do I make sure that my post has the right topic?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When a list administrator defines a topic, he or she sets three things:

a topic name
a regular expression (regexp)
a description
You can view this information by logging in to your member options page. (See Section 3.1 for more details on how to do this.) and clicking on the "details" link for any topic that interests you.

To post on a given topic, you need to make sure that the Keywords: or Subject: headers in a message match the regular expression for that topic. Regular expressions can actually be fairly complex, so you may want to just ask the list administrator if you don't know how to make heads or tails of the expression given.

Most Mailman topic expressions will be fairly simple regular expressions, so in this document we will simply give you some common examples. Regular expressions are a bit too complex to teach in a few lines here, so if you really want to understand how the regular expressions work, you should find a tutorial or reference elsewhere. (For example, DevShed has a decent tutorial at http://www.devshed.com/Server_Side/Administration/RegExp/)

Here are some examples of possible regular expressions and matching lines:

Regular expression
Matching lines
zuff
Keywords: zuff
zuff
Keywords: ZUFF
zuff
Keywords: Zuff
zuff
Keywords: amaryllis, zuff, applesauce
zuff
Subject: [zuff] Do you have the right stuff for zuff?
zuff
Subject: Do you have the right stuff for zuff?
zuff
Subject: What is zuff?
 \[zuff\]
Keywords: [zuff]
 \[zuff\]
Subject: [zuff] Do you have the right stuff?
 \[zuff\]
Subject: Online zuff tutorials (was Re: [zuff] What is zuff?)
A few notes:

The matching is case-insensitive, so if zuff matches, so will ZUFF, zuFF, and any other variations in capitalization.
Some characters have special meaning in a regular expression, so to match those characters specifically, they must be "escaped" with a backslash (). As you can see in the above example, [ and ] are such characters. (Others include ".", "?", and "*"). The backslash is also used for other things (I wasn't kidding about regular expressions being complex: consult other documentation for details about other uses of the backslash character), but this is the most likely use in a topic expression.

9.2 How do I subscribe to all or only some topics on a list?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If topics have been set up by your mailing list administrator, you can choose to subscribe to only part of a list by selecting the topics you want to receive.

If you wish to get all messages sent to the list, make sure you are not subscribed to any topics.

Log in to your member options page. (See Section 3.1 for more details on how to do this.)
Look for the section marked "Which topic categories would you like to subscribe to?"
If any topics are defined, you can select those you wish. If you do not select any topics of interest, you will receive all posts sent to the list.

You probably also want to look at Section 9.3 which discusses changing your settings for messages where no topic is set.

9.3 How do I get or avoid getting messages with no topic set?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you wish to get all messages sent to the list, make sure you are not subscribed to any specific topic. (See Section 9.2.)

If you are only subscribed to some topics, you can either choose to either receive or not receive messages with no topic set, much the way you can choose to subscribe only to certain topics.

To change this setting,

Log in to your member options page. (See Section 3.1 for more details on how to do this.)
Look for the section marked "Do you want to receive message that do not match any topic filter?"
If you wish to receive messages with no topic set, select "Yes." If you do not wish to receive such messages, choose "No."

This setting has no effect if you are not subscribed to any topics.

其他选项
-----------------------------

10.1 Change Globally? Set Globally? What does that mean?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For some of the options given in your member options page, there is a tick-box which says "Change Globally" or "Set Globally." This means that if you change this option, you can also have the change made for all your other list subscriptions with the same address to lists on the same domain. This can be handy if, for example, you want to make sure all your passwords are the same, or you are going on vacation and want to turn off mail delivery from all the lists.

修改名字
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To change your subscription name,

1. Log in to your member options page. (See Section 3.1 for more details on how to do this.)
2. In the section marked "Changing your LISTNAME membership information," enter your new name in the appropriate box.

This can also be changed for multiple lists at the same time if you are subscribed to more than one list on the same domain. See Section 10.1 for information about changing settings globally.

Note: You do not need to have a subscription name set.

设置首选语言
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Mailman is available with many different languages. (For a complete listing see http://mailman.sourceforge.net/i18n.html.) This means that, if your list has other languages enabled, you may be able to have the web interface, etc. in a language of your choice.

	
This does NOT necessarily mean that all the posts sent to the list will be in the language you selected. Only the pre-prepared texts presented by Mailman will be affected by this setting. Posts are in whatever language the poster uses.
Your preferred language is set when you subscribe (see Section5.1), and can be changed later if the list supports more than one language.

To change your preferred language in Mailman,

1. Log in to your member options page. (See Section 3.1 for instructions on how to do this.)
2. Go to the section marked "What language do you prefer?" and choose the appropriate language from the drop-down list. If there is no drop-down list of languages, the list you are on probably only supports one language.

If your list does not support the language you would prefer to use, you may contact the list administrator (LISTNAME-owner@DOMAIN) to see if it can be added, but remember that this may mean some work that the list and/or site administrator(s) do not have time or the ability to do.

If your language of choice is not available because no translation exists for Mailman, please consider volunteering your time as a translator. For more information you may want to consult the mailman-i18n mailing list at http://mail.python.org/mailman/listinfo/mailman-i18n. (i18n is a common short-hand for "internationalization" because the word starts with an i, ends with an n, and has 18 letters in between. If you mumble a bit, i18n even sounds a bit like "internationalization.")

10.4 How do I avoid having my name appear on the subscribers list? (the hide option)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you do not want to have your email address show up on the subscriber list for any reason, you can opt to have it concealed.

Common reasons for doing this include avoiding unsolicited bulk email (spam). By default, the subscribers list is obscured to hinder spam harvesters, but if you feel this is insufficient it's easy enough to remove address from the subscriber list given in the information pages or by email request. (Note that this does not conceal your address from the list administrators.) You may wish to see Section 11.2 for more information on what Mailman can do to help avoid spam.

To change this setting using the web interface:

1. Log in to your member options page. (See Section 3.1 for instructions on how to do this.)
2. Go to the section marked "Conceal yourself from subscriber list?" and choose "Yes" to hide your name from the list, or "No" to allow your name to appear on the list.

To change this setting using the email interface:

1. Send a mail to LISTNAME-request@DOMAIN with the command set hide on or set hide off.

Commands can appear in either the body or the subject of the message. (See Section 3.2 for more information about sending mail commands.)
2. Set it to "on" to conceal your email address from the membership list, or "off" to stop concealing your address

常见问题
--------------------------------

查看存档
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If the list has archives, they can be viewed by going to a web page address. This address usually linked from the list information page and can be found in the List-Archive: of every list message unless your list administrator has disabled these headers. (Many mail programs hide the List-Archive: mail header, so you may have to tell your mail program to allow you to view full headers before you will be able to see it.)

Public archives usually have addresses of the form http://WEBSERVER/pipermail/LISTNAME/ and private archives usually have addresses of the form http://WEBSERVER/mailman/private/LISTNAME.

See Section 3.1 for more information on finding the addresses of a list.

屏蔽垃圾邮件
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A technical list's archives may include answers to a range of different questions. Often, the people who have posted these answers would be happy to help someone who doesn't quite understand the answer, and don't mind giving their address out for that purpose. But although it would be wonderful if everyone could contact each other easily, we also want to make sure that the list and list archives are not abused by people who send spam.

To make a range of options available to list administrators, Mailman allows a variety of configurations to help protect email addresses. Many of these settings are optional to the list administrator, so your particular list may be set up in many ways. List administrators must walk a fine line between protecting subscribers and making it difficult for people to get in touch.

Subscriber lists
o The list administrator can choose to have the subscriber list public, viewable only to list members, or viewable only to list administrators.
o The subscriber list is shown with the addresses obscured to make it difficult for spam harvesters to collect your address.
o You can choose to have your address hidden from the subscriber list. (See Section 10.4 for more information.)
o Note: The entire subscriber list is always available to the list administrators.
List archives
o The list administrator can choose for the archives to be public, viewable only to members (private), or completely unavailable.
o The HTML archives which are created by Pipermail (the archiving program which comes default with Mailman) contain only obscured addresses. Other archiving programs are available and can do different levels of obfuscation to make addresses less readable.
o If you wish to be more sure, you can set the mail header X-no-archive: and Mailman will not archive your posts.
	
This does not stop other members from quoting your posts, possibly even including your email address.
Limited posting to the lists
o The list administrator can choose who can post to the list. Most lists are either moderated (a moderator or administrator reviews each posting), set so only subscribers may post to the list, or allow anyone to post to the list.
o By allowing only subscribers to post to a list, Mailman often blocks all spam and some viruses from being sent through the list. As such, this is a fairly common setting used by list administrators.
Anonymous lists
o Lists can also be made fully anonymous: all identifying information about the sender is stripped from the header before the message is sent on.
o This is not typically used for anti-spam measures (it has other uses), but it could be used in that way if desired.
Of course, many address-obscuring methods can be circumvented by determined people, so be aware that the protections used may not be enough.

附录
----------

Email commands quick reference

Member options quick reference
