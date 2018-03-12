---
layout: post
title: "McAfee MySQL Audit Plugin: logging to syslog-ng (Part 1)"
modified:
categories:
excerpt:
tags: [mysql-audit]
image:
  feature:
date: 2015-01-01T15:37:51-05:00
---

I finally got down to writing a post about the [McAfee MySQL Audit Plugin](https://github.com/mcafee/mysql-audit/). The project of course is maintained by the Database Security Team (which I lead) at McAfee. In the next few weeks my plan is to add some informative posts about the plugin and how it can be used. In this post I will review how to configure the plugin to log to a remote syslog server by using syslog-ng.

To start, you will need to install the McAfee MySQL Audit Plugin. Installing the plugin is straight forward and well documented at the poject's [wiki](https://github.com/mcafee/mysql-audit/wiki) (if you think the documentation can be improved please let me know). The plugin itself does not support writing to syslog but it does support logging to a unix domain socket, which is supported by syslog-ng. Thus, we will be using syslog-ng as a relay to send the logs to a remote syslog server. Note that most Linux distros don't have syslog-ng installed as default. So you may first need to install syslog-ng. On debian/Ubuntu this can be done with a simple `apt-get install syslog-ng`. More info about syslog-ng is available here: [http://syslog-ng.org](http://syslog-ng.org).

To configure the audit plugin to log to a unix domain socket you will need to set the following two global variables:

* `audit_json_socket_name=<path to unix domain socket>`
* `audit_json_socket=ON`

For testing, you can set these variables via the mysql client by executing a `set global ...` command. For production use, you will probably want to add these variables to the mysql configuration file: `my.cnf`. To check that the variables are properly set you can run the following command in mysql client:

`show global variables like '%json_socket%';`

Example outoput:


    mysql> show global variables like '%json_socket%';
    +-------------------------+---------------------------------+
    | Variable_name           | Value                           |
    +-------------------------+---------------------------------+
    | audit_json_socket       | ON                              |
    | audit_json_socket_name  | /var/lib/mysql/mysql-audit.sock |
    | audit_json_socket_retry | 10                              |
    +-------------------------+---------------------------------+

Next you will need to configure syslog-ng. Configuration of syslog-ng can be done by editing the file `/etc/syslog-ng/syslog-ng.conf` or better yet by adding a separate configuration file to the directory: `/etc/syslog-ng/conf.d/`. We will need to add the following configuration for syslog-ng to listen on the specified unix domain socket (in the example below: `/var/lib/mysql/mysql-audit.sock`) and relay the messages onwards to a remote host:

    source s_myaudit { unix-stream("/var/lib/mysql/mysql-audit.sock" flags(no-parse)); };
    destination d_myaudit_remote { tcp("remotehost" port(514)); };
    log { source(s_myaudit); destination(d_myaudit_remote); };

Note that we use the `no-parse` flag as the audit plugin does not include a syslog header and this flag tells syslog-ng to treat each line as the whole message.

If the unix domain socket is to be used by MySQL only, you can further restrict permissions on the socket by adding to the source configuration: `owner(mysql) perm(0600)`. Thus, limiting only the mysql user to write to the syslog-ng socket. Also, you may want to increase the max message size of syslog-ng for the socket. The default of 8KB may not be sufficient as mysql can generate very large audit entries. This can be done by setting `log_msg_size`. Example of setting the source configuration line with added permission limiting and a max log message of 32KB:

    source s_myaudit { unix-stream("/var/lib/mysql/mysql-audit.sock" flags(no-parse) owner(mysql) perm(0600) log_msg_size(32768)); };

This completes the setup to relay audit log entries from mysql to a remote syslog server via syslog-ng.
