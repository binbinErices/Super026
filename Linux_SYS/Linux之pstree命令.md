## Linux之pstree命令

pstree命令是用于查看进程树之间的关系，即哪个进程是父进程，哪个是子进程，可以清楚的看出来是谁创建了谁

几个重要的参数：

-A: 各进程树之间的连接以ASCII码字符来连接

-U:各进程树之间的连接以utf8字符来连接，某些终端可能会有错误

-p:同时列出每个进程的PID

-u: 同时列出每个进程的所属账号名称：

```shell
➜  ~ pstree -up
systemd(1)─┬─AliYunDun(24926)─┬─{AliYunDun}(24927)
           │                  ├─{AliYunDun}(24928)
           │                  ├─{AliYunDun}(24939)
           ├─AliYunDunUpdate(24861)─┬─{AliYunDunUpdate}(24863)
           │                        ├─{AliYunDunUpdate}(24864)
           │                        └─{AliYunDunUpdate}(24865)
           ├─agetty(2515)
           ├─agetty(2524)
           ├─aliyun-service(3246)─┬─{aliyun-service}(3249)
           │                      └─{aliyun-service}(3250)
           ├─atd(2491)
           ├─auditd(2422)───{auditd}(2427)
           ├─chronyd(2487,chrony)
           ├─crond(20481)
           ├─dbus-daemon(2474,dbus)
           ├─dhclient(2767)
           ├─firewalld(5389)───{firewalld}(5635)
           ├─httpd(13653)─┬─httpd(6567,apache)
           │              ├─httpd(21599,apache)
           │              ├─httpd(21600,apache)
           │              ├─httpd(21601,apache)
           │              ├─httpd(21602,apache)
           │              ├─httpd(21603,apache)
           │              ├─httpd(21778,apache)
           │              └─httpd(24765,apache)
           ├─mysqld(14533,mysql)─┬─{mysqld}(14538)
           │                     ├─{mysqld}(14539)
           │                     ├─{mysqld}(14540)
           ├─polkitd(2466,polkitd)─┬─{polkitd}(2485)
           │                       ├─{polkitd}(2486)
           │                       ├─{polkitd}(2490)
           │                       ├─{polkitd}(2496)
           │                       ├─{polkitd}(2501)
           │                       └─{polkitd}(2530)
           ├─rsyslogd(2833)─┬─{rsyslogd}(2851)
           │                └─{rsyslogd}(2884)
           ├─sshd(3212)─┬─sshd(29868)───zsh(29871)───app(1224)───app(1225)
           │            ├─sshd(31623)───sftp-server(31626)
           │            └─sshd(31772)───zsh(31774)───pstree(1449)
           ├─systemd-journal(1315)
           ├─systemd-logind(2465)
           ├─systemd-udevd(1333)
           ├─tuned(2830)─┬─{tuned}(3140)
           │             ├─{tuned}(3141)
           │             ├─{tuned}(3142)
           │             └─{tuned}(3161)
           └─vsftpd(6461)
```



