title: mysql start error
tags:
  - Mysql
  - Database
category:
  - Database
categories:
  - Others
date: 2015-11-02 14:41:00
---
## mysql start error ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysql.sock' (2)

When I type mysql, it shows "*ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysql.sock' (2)"*.

Then I try to start mysql service: 
`$ service mysql start`

But then I get error "
No directory, logging in with HOME=/
......
 * MySQL Community Server 5.6.27 did not start. Please check logs for more details.
"

Then I try to find the log file. It's located in /var/lib/mysql/bejond.err

Then I look at the log file and locate 
"
InnoDB: No valid checkpoint found.
InnoDB: If this error appears when you are creating an InnoDB database,
InnoDB: the problem may be that during an earlier attempt you managed
InnoDB: to create the InnoDB data files, but log file creation failed.
InnoDB: If that is the case, please refer to
InnoDB: http://dev.mysql.com/doc/refman/5.6/en/error-creating-innodb.html
"
Then I go to the link, find the note, if the error happends on start mysql,  delete all files      created by InnoDB: all      ibdata files and all      ib_logfile files.

So I delete /var/lib/mysql/ibdata1, ib_logfile0, ib_logfile1. Then restart the service. It works!