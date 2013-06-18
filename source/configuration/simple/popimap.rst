Pop/IMAP
===========

Courier IMAP
-------------------

安装:

.. code-block:: sh

   sudo aptitude install courier-base courier-authdaemon courier-authlib-mysql \ 
			courier-imap courier-imap-ssl courier-ssl

will prompt you about webdirectories. You can say no to this. It will also warn you about the certificate location. Ignore it.

Please refer to previous edition for more explanations. But below is the details of what you need to change.

.. code-block:: sh

   sudo vi /etc/courier/authdaemonrc

Change to mysql mode.

.. code-block:: ini

   authmodulelist="authmysql"

Further down enable logging.

.. code-block:: ini

   DEBUG_LOGIN=2

.. code-block:: sh

   sudo vi /etc/courier/authmysqlrc

Changed user

.. code-block:: ini

   MYSQL_USERNAME mail

Changed password to whichever you have chosen

.. code-block:: ini

   MYSQL_PASSWORD mailPASSWORD

Changed database

.. code-block:: ini

   MYSQL_DATABASE maildb

Changed users table

.. code-block:: ini

   MYSQL_USER_TABLE users

Keep commented in crypt pw

.. code-block:: ini

   MYSQL_CRYPT_PWFIELD crypt

Keep commented out clear pw

.. code-block:: ini

   # MYSQL_CLEAR_PWFIELD clear

Added maildir

.. code-block:: ini

   MYSQL_MAILDIR_FIELD concat(home,'/',maildir)

Added where clause

.. code-block:: ini

   MYSQL_WHERE_CLAUSE enabled=1

Lastly you can have a look at the imapd file, but no changes is needed.

.. code-block:: sh

   vi /etc/courier/imapd
