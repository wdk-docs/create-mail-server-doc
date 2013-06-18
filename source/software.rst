软件
=========

安装服务器需要的软件包：

* 系统: Ubuntu Linux
 
 www.ubuntu.com

 Ah the age old distro argument... Thankfully this set up should work on most distros. I used to base this howto on Mandrake(now Mandriva), and I started this new edition on a Gentoo box. But I don't have the patience for Gentoo, nor the money to stay with Mandriva Power editions. Why Ubuntu? Its free, simple and slick. As Ubuntu is derived from debian the installations used here will be apt-get based. Please refer to my other editions for details on RPM or source based installations.

* MTA（Mail Transfer Agent 邮件传输代理）: Postfix

 www.postfix.org
 
 Simple, free and slick. Yup I am a sucker for anything that works easily.	 Postfix is powerfull, well established, but not too bloated, and is security concious from the start.

* Pop(Post Office Protocol 邮局协议)/IMAP(Internet Mail Access Protocol 交互邮件访问协议): Courier IMAP

 www.courier-mta.org/imap/
 
 My first mail server installtion was with Courier. I have not found a reason to change this as again it is simple, and free.

* 数据库: MySQL

 www.mysql.com

 Although I use Firebird for my application development, (or Hibernate/C-JDBC hybrids), MySQL is well supported for the sort of lookups required in a mail server.

* 内容检测: Amavisd-new

 www.ijs.si/software/amavisd/

 Easy plug in solution for spam, virus checking etc.

* Anti-Spam(反垃圾邮件): SpamAssassin

 spamassassin.apache.org

 Powerfull renowned spam fighting tool.

* Anti-Virus(防病毒): ClamAV

 www.clamav.net

 Free virus scanner that can be trusted and includes update daemon.

* 身份验证: Cyrus SASL

 www.imc.org/ietf-sasl/

 Secure and trusted crypthography technology for authentication of SMTP traffic.

* PostGrey(Postfix执行灰名单的策略服务器)

 isg.ee.ethz.ch/tools/postgrey/

 Postgrey is an excellent little script to stop 99% of all spam. All it does is on first contact for specific from-to combinations, tells the sender server to try again in a little while, which most spammers cant afford to do. When proper servers try again after a few minutes it lets it through.

* 加密: TLS

 www.ietf.org/html.charters/tls-charter.html

 Secure and trusted crypthography technology for encryption of SMTP traffic. Not too be confused with client encryption technology like GnuPG and S/MIME. They are covered in the extend section. Formerly referenced as SSL.

* WebMail: SquirrelMail or Roundcube

 www.squirrelmail.org

 Easy to set up php based web mail client. Extensive plugin selection.

 www.roundcube.net

 Ajaxified prettier web mail client. Not quite as solid as SquirrelMail.

* 平台: Amazon ec2

 aws.amazon.com/ec2

 This guide can be installed locally, co-located or in the cloud.

My preference is ec2, and I provide ec2 based examples, however it makes no difference where you install your mail server.

Please see software links appendix for further information about these software packages. In that section there is more links to documentation or forums, and viable alternatives, downloadable packages, versions details etc.

Further software and tweaks are discussed in the extension section.
Also review other peoples opinion on these packages via my references.
