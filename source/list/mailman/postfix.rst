集成postfix
==================

This instruction set is written for those who wish to integrate Mailman with the postfix MTA.  The documentation currently available does not cover in detail how to use virtual hosts and MySQL-based aliases.

.. note:: The instructions here are based on the Ubuntu 10.10 Linux distribution, loaded on a VPS.  Specific file locations may differ.

Before beginning, postfix should be installed and working correctly.  If you are using or plan to use virtual hosts, set this up prior to installing and configuring Mailman.

正常postfix集成
-----------------------------------

If you do not have any subdomains or do not plan on using virtual hosts within postfix, follow the steps below to integrate Mailman with your system.

在文件 *$prefix/Mailman/mm_cfg.py* 下添加:

.. code-block:: python

   MTA = 'Postfix'

.. note:: that depending on your specific distribution, this setting may be set within $prefix/Mailman/Defaults.py.

运行如下命令来初始化您的mailman别名:

.. code-block:: sh

   $prefix/bin/genaliases

此命令创建两个文件: **aliases** 和 **aliases.db**. 文件创建在 *$prefix/data* 目录, 但是可能因为系统设置不同可能在其他地方. 通常可以在这里找到 */var/lib/mailman/data* .

确认这些文件的用户和组都是 **mailman**

postfix与虚拟域名集成（哈希映射）
---------------------------------------------

Let's assume you have several domains (in this case example1.com and example2.net) and you plan on hosting mailman on lists.example1.com and lists.example2.net.  You will have to select a default domain you want to host mailman on.  It doesn't really matter which one you choose but remember that it will be the one where everyone will come to administer their lists.  In this case, I will select lists.example1.com

#. 在 *$prefix/Mailman/mm_cfg.py* 文件里, 在 *MTA* 那行下添加以下行:

   .. code-block:: python

      POSTFIX_STYLE_VIRTUAL_DOMAINS = ['example1.com', 'example2.net']
      VIRTUAL_MAILMAN_LOCAL_DOMAIN = 'localhost'
      
#. 修改 **DEFAULT_EMAIL_HOST** 和 **DEFAULT_URL_HOST** 匹配你的默认域名:

   .. code-block:: python

      #-------------------------------------------------------------
      # Default domain for email addresses of newly created MLs
      DEFAULT_EMAIL_HOST = 'lists.mikesoh.com'
      #-------------------------------------------------------------
      # Default host for web interface of newly created MLs
      DEFAULT_URL_HOST   = 'lists.mikesoh.com'

#. 保存关闭 *mm_cfg.py* 并运行 **$prefix/bin/genaliases**, taking care to make sure that the resulting alias files are readable by postfix.

#. 修改 **postfix** 配置，包含 mailman 别名.  在 *main.cf* 里, 添加别名文件:

   .. code-block:: python

      virtual_alias_maps = mysql:/etc/postfix/virtual/existing_aliases.cf,
               hash:/var/lib/mailman/data/virtual-mailman
      alias_maps = hash:/etc/aliases, hash:/var/lib/mailman/data/aliases

   .. Warning:: The above additions to virtual_alias_maps and alias_maps are not used by postfix_to_mailman.py and can potentially cause conflicts within Postfix over delivery of mail to Mailman.

      The GNU-Mailman project recommends the methods outlined in section 6.1.1 and section 6.1.2 of the Installation Manual for integrating Postfix and Mailman with virtual domains.

#. 保存和关闭 *main.cf* . 打开 *master.cf* 文件. If not already included, add the mailman service to the end of the file:

   .. code-block:: python

      # ==========================================================================
      # service type  private unpriv  chroot  wakeup  maxproc command + args
      #               (yes)   (yes)   (yes)   (never) (100)
      # ==========================================================================
      mailman   unix  -       n       n       -       -       pipe
      flags=FR user=list argv=/usr/lib/mailman/bin/postfix-to-mailman.py
      ${nexthop} ${user}

#. 保存和关闭 *master.cf*. 打开你的 *transport* 文件. By default, this file should be located within the same directory as your main.cf and master.cf files. Add the following lines:

   .. code-block:: python

      example1.com   mailman:
      example2.net   mailman:

#. 在 *transport* 文件运行 **postmap -v**.

   .. code-block:: sh

      postmap -v /etc/postfix/transport

#. 重启 **mailman** 和 **postfix**

在web界面不能创建列表. 新列表必须使用命令 **$prefix/bin/newlist**.
