管理手册
================

介绍
------------------

This is one of a set of three documents for the various people involved in running and using a mailing list. For the members of mailing lists (the subscribers), we have the Mailman 2.1 Members Manual which covers things like subscribing and unsubscribing. For the people who install and set up Mailman, we will eventually have the Mailman 2.1 Site Administration Manual, which has not yet been written.

This document covers the people who run mailing lists: the list administrators (who can change all the list options, as well as authorize postings that have been held for moderation), and the list moderators (who cannot modify list settings).

This document does not need to be read in order, so if you just want an answer to a specific question, jump to the appropriate place and references to other sections will be provided when necessary or potentially helpful.

致谢
^^^^^^^^^^^^^^^^^^^^^^^^

The bulk of this document has been written by Terri Oda. Terri used to get a lot of spam, so she did research into neat evolutionary anti-spam solutions. And then she thought it would be nice if her grandfather could use the web without worrying about bad stuff getting into his computer, so she started doing web security. All of this is a roundabout way of saying that her day job is as a security researcher, and she actually doesn't consider herself a document writer, but she was tired of Mailman not having documentation so she figured it was time to do something about it.

The original site admin docs were written by Barry Warsaw. Thanks also go to the rest of the Mailman team who provided the inline help upon which much of this documentation is based.

This document is now stored in the Mailman wiki http://wiki.list.org, and may be edited and added to by members of the Mailman community. Please see the wiki changelogs for more information.

Mailman's 界面
-----------------------
Mailman有3个界面:

* Web界面 – used by most people, this is the main interface most users will see
* email界面 – used as an alternative to the web interface
* 命令行界面 – used mostly by site admins, this is very powerful but perhaps not so user-friendly

Web界面
^^^^^^^^^^^^^^^^^^^^^^^

The most commonly used interface of Mailman is the web interface. Almost all the options can be set from this interface, and it also provides inline help and descriptions of each option. More importantly, the day-to-day tasks of running a mailing list are handed through this interface.

Assuming a fairly standard configuration, the configuration interface has a URL like this:

http://WEBSERVER/mailman/admin/LISTNAME

And the moderation interface has a very similar URL as follows:

http://WEBSERVER/mailman/admindb/LISTNAME

	
The web domain (WEBSERVER) can't be set from the web, since it's a bit too easy to render your web interface unusable by making a typo. As such, this setting is usually changed from the command line. The fix_url script is provided for this purpose. It is used in conjuction with withlist as follows:

bin/withlist -l -r fix_url listname [options]
In many cases, this is the only interface a list administrator will use, and may be the only interface the list administrator has permissions to use.

email界面
^^^^^^^^^^^^^^^^^^^^^^^^^

There is also an email interface for Mailman, allowing list admins to adjust settings or handle caught messages without requiring them to open up a web browser.

	
Since this interface is not as commonly used as the web interface, it is highly likely that this part of the documentation will be among the last pieces written. If you wish to speed this up, please feel free to help fill it in!

适度
-----------------

The web interface for moderation can be found at a URL similar to the following, depending on your mailman config:

http://WEBSERVER/mailman/admindb/LISTNAME

From here, you will see a list of anything awaiting your decision. This is largely messages that have been caught for some reason, but may also include subscription/unsubscription requests if you require admin authorization before someone can join or leave the list.

为什么我要授权这些消息?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Messages are "caught" in the moderation queue depending upon your list settings. There are a number of rules that allow you to choose which messages require moderation, but basically you want things which might be inappropriate to be caught by these filters so a human can choose whether they get sent to all subscribers or not.

For example, as an anti-spam measure, many lists only accept messages from their members. Any non-member who sends a message will have it caught in the moderation queue. If the message turns out to be spam, it can be discarded, but if it turns out to be from someone posting from their work address, you may wish to send it out anyhow.

	FIXME
Put a link to the relevant section here

我如何中度消息?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For each message displayed in the queue, Mailman will give a summary of the message. This includes the following:

The sender's address
The subject of the message
The size
The reason the message was held for moderation (eg: "Post by non-member to a members-only list")
The date/time the message was received
A link is provided so you can read the entire message if you want to.

There are 4 choices when it comes to moderating a message:

Defer
This option allows you to put off the choice until later. It leaves the message in the queue and does nothing to it.
Accept
This accepts the message, allowing it to be sent to all of your subscribers.
Reject
This rejects the message, letting the sender know that it was rejected. You may also include a message explaining why. The reject option is usually used for messages which are inappropriate for that list for some reason – they may have attachments which are too large, be off-topic, or from someone who is not allowed to post to that list. Rejected messages are not sent to the list.
Discard
This discards the message, ensuring that it is not sent to the list. In this case, no message is sent to the sender. This option is most often used for spam messages.
In addition, there is a checkbox (near the submit button) which allows you to "Discard all messages marked Defer" – this allows you to bulk-discard spam messages and can be a very handy option. It only applies to the messages displayed in the queue page as you've loaded it.

为什么我要授权订阅/退订?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is a setting chosen when the list was set up. If you do not wish to moderate subscription/unsubscription requests, you can change your list config.

 FIXME

Put a link to the relevant section here

我该如何处理订阅/退订
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The interface for moderating members joining and leaving is similar to that for messages, only instead of a message summary we have the email address and name of the person under consideration.

The options for subscriptions and unsubscriptions are the same as those for messages:

* Defer
 Decide later.
* Accept
 Allow this person to subscribe or unsubscribe
* Reject
 Reject this person's request, letting them know and optionally giving them a reason for the rejection.
* Discard
 Discard this person's request silently, dropping it from the queue without notifying the person.

配置
------------------------

通用选项
^^^^^^^^^^^^^^^^^^^^^^^

General List Personality

密码
^^^^^^^^^^^^^^^^^^^^^^^

语言设置
^^^^^^^^^^^^^^^^^^^^^^^

会员管理
^^^^^^^^^^^^^^^^^^^^^^^

非消化选项
^^^^^^^^^^^^^^^^^^^^^^^

精华选项
^^^^^^^^^^^^^^^^^^^^^^^

隐私选项
^^^^^^^^^^^^^^^^^^^^^^^

弹跳处理
^^^^^^^^^^^^^^^^^^^^^^^

归档选项
^^^^^^^^^^^^^^^^^^^^^^^

邮件<->新闻网关
^^^^^^^^^^^^^^^^^^^^^^^

自动应答
^^^^^^^^^^^^^^^^^^^^^^^

内容过滤
^^^^^^^^^^^^^^^^^^^^^^^

删除邮件列表
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Removing/deleting mailing lists is performed with the command line tool rmlist. rmlist has 2 modes of operating:

Only remove the list from the admin interface. This retains archives on file system (that is, postings) but makes the list completely inaccessible to users, this removes:
The list itself from the web admin interface as well as for users of the list
List meta data:
Name, description, etc.
Policies in place (for example, list moderation settings)
List members
Like #1 above but also removes the list archives
If list archives are retained (default behavior), the archive files are left on the file system. The Mailman administrator can still access the archives through web interface (if the URL has been bookmarked) but list users (and the list administrator) will not have access unless another web service is making the list archives accessible (for example through a search engine).

NOTE removing the list completely requires manual editing of the mail aliases file, that is, /etc/aliases

Once a list has been removed, it may be re-added with the newlist command line tool, however all information about the list (for example, description and previous members) will be missing. However the archives will then be available to all users via the web interface.

It is also possible to allow mailing list removal with the option/variable OWNERS_CAN_DELETE_THEIR_OWN_LISTS in mm_cfg.py, by default this option is disabled.

主题
^^^^^^^^^^^^^^^^^^^^^^^

