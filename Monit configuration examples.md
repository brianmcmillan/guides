#Monit configuration

##Elasticsearch
```
check process elasticsearch with pidfile /var/run/elasticsearch.pid
  start program = "/etc/init.d/elasticsearch restart" with timeout 60 seconds
  stop program  = "/etc/init.d/elasticsearch stop"
  if cpu > 90% for 15 cycles then alert
  if totalmem > 90% for 15 cycles then alert
  if loadavg(15min) greater than 10 for 50 cycles then alert
  group elasticsearch
```


https://lists.nongnu.org/archive/html/monit-general/2007-09/msg00081.html

##SSHd
```
check process sshd with pidfile /var/run/sshd.pid
   start program  "/etc/init.d/sshd start"
   stop program  "/etc/init.d/sshd stop"
   if failed port 22 protocol ssh then restart
```

##INETd
```
Inetd (internet service manager)
 check process inetd with pidfile /var/run/inetd.pid
   start program = "/etc/init.d/inetd start"
   stop program = "/etc/init.d/inetd stop"
   if failed host 192.168.1.1 port 25 protocol smtp then restart  # e.g. exim 
   if failed host 192.168.1.1 port 515 then restart               # e.g. cups-lpd
   if failed host 192.168.1.1 port 113 then restart               # e.g. ident
```

##Syslogd (system logfile daemon)
```
 check process syslogd with pidfile /var/run/syslogd.pid
   start program = "/etc/init.d/sysklogd start"
   stop program = "/etc/init.d/sysklogd stop"

 check file syslogd_file with path /var/log/syslog
   if timestamp > 65 minutes then alert # Have you seen "-- MARK --"?
```

##NTP (time server)
 check process ntpd with pidfile /var/run/ntpd.pid
   start program = "/etc/init.d/ntpd start"
   stop  program = "/etc/init.d/ntpd stop"
   if failed host 127.0.0.1 port 123 type udp then alert
   
   
##Postfix (mail server)
 check process postfix with pidfile /var/spool/postfix/pid/master.pid
   group mail
   start program = "/etc/init.d/postfix start"
   stop  program = "/etc/init.d/postfix stop"
   if failed port 25 protocol smtp then restart
   depends on postfix_rc

 check file postfix_rc with path /etc/init.d/postfix
   group mail
   if failed checksum then unmonitor
   if failed permission 755 then unmonitor
   if failed uid root then unmonitor
   if failed gid root then unmonitor
   
##Node.js
http://howtonode.org/deploying-node-upstart-monit
check process nodejs with pidfile "/var/run/yourprogram.pid"
    start program = "/sbin/start yourprogram"
    stop program  = "/sbin/stop yourprogram"
    if failed port 8000 protocol HTTP
        request /
        with timeout 10 seconds
        then restart
        
https://codeplease.wordpress.com/2013/09/27/deploying-node-js-production/
https://blog.noort.be/2011/03/25/node-js-with-upstart-and-monit.html



##HAProxy
check process haproxy with pidfile /var/run/haproxy.pid
  alert bedis9@gmail.com only on { timeout, nonexist }
  start program = "/etc/init.d/haproxy start"
  stop program  = "/etc/init.d/haproxy stop"
  

##DNSmasq
 check process dnsmasq with pidfile /var/run/dnsmasq/dnsmasq.pid
  start program = "/etc/init.d/dnsmasq start" with timeout 60 seconds
  stop program = "/etc/init.d/dnsmasq stop"
  if failed
    host 127.0.0.1
    port 53 use type udp
    protocol dns
    with timeout 60 seconds
  then restart
  if 3 restarts within 5 cycles then timeout
  depends on dnsmasq-bin
  depends on dnsmasq-hosts


check file dnsmasq-bin with path /usr/sbin/dnsmasq
   if failed checksum then unmonitor
   if failed permission 755 then unmonitor
   if failed uid root then unmonitor
   if failed gid root then unmonitor

check file dnsmasq-hosts with path /etc/hosts
   if changed timestamp then restart                