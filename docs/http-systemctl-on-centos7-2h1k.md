# CentOS7 上的 http systemctl

> 原文：<https://dev.to/psnebc/http-systemctl-on-centos7-2h1k>

[Imgur](https://i.imgur.com/d4yVfaC.png)

```
[psnebc@localhost ~]$ su -
Passwort: 
Letzte Anmeldung: Freitag, den 20\. April 2018, 10:41:59 CEST auf tty1
[root@localhost ~]# systemctl start httpd 
[root@localhost ~]# systemctl enable httpd
[root@localhost ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-04-20 10:36:49 CEST; 1h 15min ago
     Docs: man:httpd(8)
           man:apachectl(8)
 Main PID: 1008 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─1008 /usr/sbin/httpd -DFOREGROUND
           ├─2312 /usr/sbin/httpd -DFOREGROUND
           ├─2313 /usr/sbin/httpd -DFOREGROUND
           ├─2314 /usr/sbin/httpd -DFOREGROUND
           ├─2315 /usr/sbin/httpd -DFOREGROUND
           └─2316 /usr/sbin/httpd -DFOREGROUND

Apr 20 10:36:48 localhost.localdomain systemd[1]: Starting The Apache HTTP Server...
Apr 20 10:36:48 localhost.localdomain httpd[1008]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using loc... message
Apr 20 10:36:49 localhost.localdomain systemd[1]: Started The Apache HTTP Server.
Apr 20 11:10:01 localhost.localdomain httpd[2308]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using loc... message
Apr 20 11:10:01 localhost.localdomain systemd[1]: Reloaded The Apache HTTP Server.
Hint: Some lines were ellipsized, use -l to show in full.
[root@localhost ~]# firewall-cmd --zone=public --permanent --add-service=http
Warning: ALREADY_ENABLED: http
success
[root@localhost ~]# firewall-cmd --zone=public --permanent --add-service=https
Warning: ALREADY_ENABLED: https
success
[root@localhost ~]# firewall-cmd --reload
success
[root@localhost ~]# systemctl restart httpd.service
[root@localhost ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-04-20 11:55:30 CEST; 4s ago
     Docs: man:httpd(8)
           man:apachectl(8)
  Process: 3742 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=0/SUCCESS)
 Main PID: 3747 (httpd)
   Status: "Processing requests..."
   CGroup: /system.slice/httpd.service
           ├─3747 /usr/sbin/httpd -DFOREGROUND
           ├─3748 /usr/sbin/httpd -DFOREGROUND
           ├─3749 /usr/sbin/httpd -DFOREGROUND
           ├─3750 /usr/sbin/httpd -DFOREGROUND
           ├─3751 /usr/sbin/httpd -DFOREGROUND
           └─3752 /usr/sbin/httpd -DFOREGROUND

Apr 20 11:55:30 localhost.localdomain systemd[1]: Starting The Apache HTTP Server...
Apr 20 11:55:30 localhost.localdomain httpd[3747]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using loc... message
Apr 20 11:55:30 localhost.localdomain systemd[1]: Started The Apache HTTP Server.
Hint: Some lines were ellipsized, use -l to show in full. 
```

Enter fullscreen mode Exit fullscreen mode