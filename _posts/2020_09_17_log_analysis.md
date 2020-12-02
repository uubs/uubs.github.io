# Log File Analysis

---

## Notes

Most log file store in /var/log

### 日志的级别

TRACE --> DEBUG --> INFO --> WARN --> ERROR --> FATAL 

### important log file in linux

1. authentication log(in binary format)

   ```/var/log/auth.log```

   ```/var/log/wtmp.log```

   ```/var/log/lastlog.log```

2. other important log files

   ```/var/log/messages.log```

   ```/var/log/dmesg.log```

   ```/var/log/mysqld.log```

   ```/var/log/daemon.log```

   ```/var/log/samba/log.nmbd```

   ```/var/log/samba/log.smbd```

### TODO

[log](https://static1.squarespace.com/static/5047a5a6e4b0dcecada15549/t/57bb138db3db2bafdfa046b2/1471878035520/BRO2016_NSF_summit_security_log_analysis_and_Bro.pdf)

### 解决方案

1. Log4j-Commons-Logging
2. LogBack-SLF4J



## words don't know

- aggregate 集中，聚合
- troubleshoot 排查，检修
- mundane 单调的