## Linux编程之ps命令详解

linux上进程有5种状态:

- 运行(正在运行或在运行队列中等待)

- 中断(休眠中, 受阻, 在等待某个条件的形成或接受到信号)

- 不可中断(收到信号不唤醒和不可运行, 进程必须等待直到有中断发生)

- 僵死(进程已终止, 但进程描述符存在, 直到父进程调用wait4()系统调用后释放)

- 停止(进程收到SIGSTOP, SIGSTP, SIGTIN, SIGTOU信号后停止运行运行)

 

```sh
1）ps a   显示现行终端机下的所有程序，包括其他用户的程序。
2）ps -A  显示所有程序。
3）ps c   列出程序时，显示每个程序真正的指令名称，而不包含路径，参数或常驻服务的标示。
4）ps -e  此参数的效果和指定"A"参数相同。
5）ps e   列出程序时，显示每个程序所使用的环境变量。
6）ps f   用ASCII字符显示树状结构，表达程序间的相互关系。
7）ps -H  显示树状结构，表示程序间的相互关系。
8）ps -N  显示所有的程序，除了执行ps指令终端机下的程序之外。
9）ps s   采用程序信号的格式显示程序状况。
10）ps S  列出程序时，包括已中断的子程序资料。
11）ps -t <终端机编号> 　指定终端机编号，并列出属于该终端机的程序的状况。
12）ps u  以用户为主的格式来显示程序状况。
13）ps x  显示所有程序，不以终端机来区分。
14）ps -l 较长,较详细显示该PID的信息 
```

 

```sh
➜  ~ ps -lA|more
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0     1     0  0  80   0 - 33408 ep_pol ?        00:25:08 systemd
1 S     0     2     0  0  80   0 -     0 kthrea ?        00:00:00 kthreadd
1 S     0     3     2  0  80   0 -     0 smpboo ?        00:01:00 ksoftirqd/0
1 S     0     5     2  0  60 -20 -     0 worker ?        00:00:00 kworker/0:0H
1 S     0     7     2  0 -40   - -     0 smpboo ?        00:00:00 migration/0
1 S     0     8     2  0  80   0 -     0 rcu_gp ?        00:00:00 rcu_bh
1 R     0     9     2  0  80   0 -     0 -      ?        00:24:59 rcu_sched
1 S     0    10     2  0  60 -20 -     0 rescue ?        00:00:00 lru-add-drain
5 S     0    11     2  0 -40   - -     0 smpboo ?        00:01:37 watchdog/0
```

**相关选项意思：**

|  符号   |含义   |
|---|----|
| F   | 进程的标志（flag）, 4表示用户为超级用户 |
|S |进程的状态(stat）, 各STAT的意义见下文|
|PID| 进程ID|
|C|    CPU使用资源的百分比|
|PRI| priority(优先级)的缩写|
|NI|   NICE值，进程的NICE值，数值大，表示较少占用CPU时间|
|ADDR| 核心功能，指出该进程在內存的那一部分，如果是运行的进程，一般都是“-“|
|SZ|      用掉的內存的大小|
|WCHAN| 当前进程是否正在运行， “-”表示正在运行 |
|TTY |   登陆者的终端位置|
|TIME|   用掉的CPU的时间|
|CMD | 所执行的指令|

```shell
STAT状态位常见的状态字符：
D 无法中断的休眠状态（通常 IO 的进程）
R 正在运行可中在队列中可过行的
S 处于休眠状态
T 停止或被追踪
W 进入内存交换 （从内核2.6开始无效）
X 死掉的进程(基本很少见)
Z 僵尸进程；
< 优先级高的进程
N 优先级较低的进程
L 有些页被锁进内存
s 进程的领导者（在它之下有子进程）
l 多进程的（使用 CLONE_THREAD, 类似 NPTL pthreads）
+ 位于后台的进程组
```



 ```shell
# ps -aux |more
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 133632  3188 ?        Ss    2019  25:08 /usr/lib/systemd/systemd --system --deserialize 17
root         2  0.0  0.0      0     0 ?        S     2019   0:00 [kthreadd]
root         3  0.0  0.0      0     0 ?        S     2019   1:00 [ksoftirqd/0]
root         5  0.0  0.0      0     0 ?        S<    2019   0:00 [kworker/0:0H]
root         7  0.0  0.0      0     0 ?        S     2019   0:00 [migration/0]
root         8  0.0  0.0      0     0 ?        S     2019   0:00 [rcu_bh]
root         9  0.0  0.0      0     0 ?        R     2019  24:59 [rcu_sched]
root        10  0.0  0.0      0     0 ?        S<    2019   0:00 [lru-add-drain]
root        11  0.0  0.0      0     0 ?        S     2019   1:37 [watchdog/0]
root        13  0.0  0.0      0     0 ?        S     2019   0:00 [kdevtmpfs]
root        14  0.0  0.0      0     0 ?        S<    2019   0:00 [netns]
root        15  0.0  0.0      0     0 ?        S     2019   0:04 [khungtaskd]
root        16  0.0  0.0      0     0 ?        S<    2019   0:00 [writeback]
root        17  0.0  0.0      0     0 ?        S<    2019   0:00 [kintegrityd]
root        18  0.0  0.0      0     0 ?        S<    2019   0:00 [bioset]
root        19  0.0  0.0      0     0 ?        S<    2019   0:00 [bioset]
root        20  0.0  0.0      0     0 ?        S<    2019   0:00 [bioset]
root        21  0.0  0.0      0     0 ?        S<    2019   0:00 [kblockd]
root        22  0.0  0.0      0     0 ?        S<    2019   0:00 [md]
root        23  0.0  0.0      0     0 ?        S<    2019   0:00 [edac-poller]
root        24  0.0  0.0      0     0 ?        S<    2019   0:00 [watchdogd]
root        30  0.0  0.0      0     0 ?        S     2019   0:04 [kswapd0]
root        31  0.0  0.0      0     0 ?        SN    2019   0:00 [ksmd]
root        32  0.0  0.0      0     0 ?        SN    2019   0:55 [khugepaged]
root        33  0.0  0.0      0     0 ?        S<    2019   0:00 [crypto]
root        41  0.0  0.0      0     0 ?        S<    2019   0:00 [kthrotld]
root        43  0.0  0.0      0     0 ?        S<    2019   0:00 [kmpath_rdacd]
root        44  0.0  0.0      0     0 ?        S<    2019   0:00 [kaluad]
root        45  0.0  0.0      0     0 ?        S<    2019   0:00 [kpsmoused]
 ```

**相关选项意思：**

|  符号   |含义   |
|---|----|
| USER  | 进程的属主 |
|PID|进程的ID|
|PPID|    父进程|
|%CPU|  进程占用的CPU百分比|
|%MEM|占用内存的百分比|
|NI|  进程的NICE值，数值大，表示较少占用CPU时间|
|VSZ | 进程使用的虚拟內存量（KB）|
|RSS  |该进程占用的固定內存量（KB）（驻留中页的数量）|
|TTY| 该进程在哪个终端上运行(登陆者终端的位置)， 若与终端机无关，则显示 ?。另外， tty1-tty6 是本机上面的登入者程序，若为 pts/0 等等的，则表示为由网络连接进主机的程序|
|STAT|     该进程的状态|
|START |   该进程被触发启动的时间|
|TIME |     该进程实际使用CPU运行的时间|
|COMMAND| 命令的名称和参数|