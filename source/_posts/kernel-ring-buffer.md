---
title: kernel ring buffer
date: 2018-05-21 18:40:20
tags:
  - 读书笔记
  - 技术
  - Linux
---

```
	/var/log/dmesg Contains kernel ring buffer information.

	/var/log/kern.log Contains only the kernel's messages of any loglevel

	/var/log/user.log Contains information about all user level logs
```

All of this has to do with logging. No, none of it has to do with runlevel or "protection ring".

The kernel keeps its logs in a ring buffer. The main reason for this is so that the logs from the system startup get saved until the syslog daemon gets a chance to start up and collect them. Otherwise there would be no record of any logs prior to the startup of the syslog daemon. The contents of that ring buffer can be seen at any time using the dmesg command, and its contents are also saved to /var/log/dmesg just as the syslog daemon is starting up.

All logs that do not come from the kernel are sent as they are generated to the syslog daemon so they are not kept in any buffers. The kernel logs are also picked up by the syslog daemon as they are generated but they also continue to be saved (unnecessarily, arguably) to the ring buffer.

The log levels can be seen documented in the syslog(3) manpage and are as follows:


* LOG_EMERG: system is unusable
* LOG_ALERT: action must be taken immediately
* LOG_CRIT: critical conditions
* LOG_ERR: error conditions
* LOG_WARNING: warning conditions
* LOG_NOTICE: normal, but significant, condition
* LOG_INFO: informational message
* LOG_DEBUG: debug-level message


Each level is designed to be less "important" than the previous one. A log file that records logs at one level will also record logs at all of the more important levels too.

The difference between /var/log/kern.log and /var/log/mail.log (for example) is not to do with the level but with the facility, or category. The categories are also documented on the manpage.

See also
--------


* [What are the concepts of “kernel ring buffer”, “user level”, “log level”?](https://unix.stackexchange.com/questions/198178/what-are-the-concepts-of-kernel-ring-buffer-user-level-log-level)
