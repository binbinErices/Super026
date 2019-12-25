## open函数O_CLOEXEC模式和fcntl函数FD_CLOEXEC选项

[TOC]

### 1. open函数O_CLOEXEC模式

```c
 #include <sys/types.h>
 #include <sys/stat.h>
 #include <fcntl.h>

 int open(const char *pathname, int flags);
 int open(const char *pathname, int flags, mode_t mode);
```

关于<font color = "red">**O_CLOEXEC**</font>在man帮助手册中的解释

>O_CLOEXEC (Since Linux 2.6.23)
>              Enable  the  close-on-exec  flag  for the new file descriptor.  Specifying this flag permits a program to avoid additional fcntl(2) F_SETFD operations to set the FD_CLOEXEC flag.  Additionally, use of this flag is essential in some multithreaded programs since using a separate fcntl(2) F_SETFD operation to set the FD_CLOEXEC flag does not suffice to avoid race conditions where one thread opens a file descriptor at the same time as another thread does a fork(2) plus execve(2).

---

> 为新文件描述符启用close-on-exec标志。 指定此标志使程序可以避免其他fcntl（2）中F_SETFD操作来设置FD_CLOEXEC标志。 此外，在某些多线程程序中，必须使用此标志，因为使用单独的fcntl（2）F_SETFD操作设置FD_CLOEXEC标志不足以避免竞争情况，在这种情况下，一个线程与另一个线程同时打开一个文件描述符 fork（2）加上execve（2）。

- 调用open函数O_CLOEXEC模式打开的文件描述符在执行exec调用新程序中关闭，且为原子操作.
- 调用open函数不使用O_CLOEXEC模式打开的文件描述符，然后调用fcntl 函数设置FD_CLOEXEC选项，效果和使用O_CLOEXEC选项open函数相同，但分别调用open、fcntl两个函数，不是原子操作，多线程环境中存在竞态条件，故用open函数O_CLOEXEC选项代替之.
- 调用open函数O_CLOEXEC模式打开的文件描述符，或是使用fcntl设置FD_CLOEXEC选项，这二者得到（处理）的描述符在通过fork调用产生的子进程中均不被关闭.
- 调用dup族类函数得到的新文件描述符将清除O_CLOEXEC模式.



### 2. fcntl函数FD_CLOEXEC选项

```c
 #include <unistd.h>
 #include <fcntl.h>

 int fcntl(int fd, int cmd, ... /* arg */ );
```

- fcntl()针对(文件)描述符提供控制。参数fd是被参数cmd操作(如下面的描述)的描述符。针对cmd的值，fcntl能够接受第三个参数int arg.
- fcntl()的返回值与命令有关。如果出错，所有命令都返回－1，如果成功则返回某个其他值。下列三个命令有特定返回值：F_DUPFD , F_GETFD , F_GETFL以及F_GETOWN。
      F_DUPFD   返回新的文件描述符
      F_GETFD   返回相应标志
      F_GETFL , F_GETOWN   返回一个正的进程ID或负的进程组ID

- fcntl函数有5种功能： 
  - 复制一个现有的描述符(cmd=F_DUPFD). 
  - 获得／设置文件描述符标记(cmd=F_GETFD或F_SETFD). 
  - 获得／设置文件状态标记(cmd=F_GETFL或F_SETFL). 
  - 获得／设置异步I/O所有权(cmd=F_GETOWN或F_SETOWN). 
  - 获得／设置记录锁(cmd=F_GETLK , F_SETLK或F_SETLKW).

**(1) cmd值的F_DUPFD ：** 
F_DUPFD    返回一个如下描述的(文件)描述符：
        最小的大于或等于arg的一个可用的描述符
        与原始操作符一样的某对象的引用
        如果对象是文件(file)的话，则返回一个新的描述符，这个描述符与arg共享相同的偏移量(offset)
        相同的访问模式(读，写或读/写)
        相同的文件状态标志(如：两个文件描述符共享相同的状态标志)
        与新的文件描述符结合在一起的close-on-exec标志被设置成交叉式访问execve(2)的系统调用

实际上调用dup(oldfd)；
等效于
        fcntl(oldfd, F_DUPFD, 0);

而调用dup2(oldfd, newfd)；
等效于
        close(oldfd)；
        fcntl(oldfd, F_DUPFD, newfd)；

>函数dup 允许你复制一个oldfd文件描述符。存入一个已存在的文件描述符，它就会返回一个与该描述符“相同”的新的文件描述符。即这两个描述符共享相同的内部结构，共享所有的锁定，读写位置和各项权限或flags等等。例如：对一个文件描述符进行了lseek操作，另一个文件描述符的读写位置也会随之改变。不过，文件描述符之间并不共享close-on-exec flags.

**(2) cmd值的F_GETFD和F_SETFD：**      

F_GETFD    取得与文件描述符fd联合的close-on-exec标志，类似FD_CLOEXEC。如果返回值和FD_CLOEXEC进行与运算结果是0的话，文件保持交叉式访问exec()，否则如果通过exec运行的话，文件将被关闭(arg 被忽略)        

F_SETFD    <font color = "red">设置close-on-exec标志，该标志以参数arg的FD_CLOEXEC位决定，应当了解很多现存的涉及文件描述符标志的程序并不使用常数 FD_CLOEXEC，而是将此标志设置为0(系统默认，在exec时不关闭)或1(在exec时关闭)   </font>

在修改文件描述符标志或文件状态标志时必须谨慎，先要取得现在的标志值，然后按照希望修改它，最后设置新标志值。不能只是执行F_SETFD或F_SETFL命令，这样会关闭以前设置的标志位。 

**(3) cmd值的F_GETFL和F_SETFL：**   

F_GETFL    取得fd的文件状态标志，如同下面的描述一样(arg被忽略)，在说明open函数时，已说明
了文件状态标志。不幸的是，三个存取方式标志 (O_RDONLY , O_WRONLY , 以及O_RDWR)并不各占1位。(这三种标志的值各是0 , 1和2，由于历史原因，这三种值互斥 — 一个文件只能有这三种值之一。) 因此首先必须用屏蔽字O_ACCMODE相与取得存取方式位，然后将结果与这三种值相比较。       
F_SETFL    设置给arg描述符状态标志，可以更改的几个标志是：O_APPEND，O_NONBLOCK，O_SYNC 和 O_ASYNC。而fcntl的文件状态标志总共有7个：O_RDONLY , O_WRONLY , O_RDWR , O_APPEND , O_NONBLOCK , O_SYNC和O_ASYNC

可更改的几个标志如下面的描述：
    O_NONBLOCK   非阻塞I/O，如果read(2)调用没有可读取的数据，或者如果write(2)操作将阻塞，则read或write调用将返回-1和EAGAIN错误
    O_APPEND     强制每次写(write)操作都添加在文件大的末尾，相当于open(2)的O_APPEND标志
    O_DIRECT     最小化或去掉reading和writing的缓存影响。系统将企图避免缓存你的读或写的数据。如果不能够避免缓存，那么它将最小化已经被缓存了的数据造成的影响。如果这个标志用的不够好，将大大的降低性能
    O_ASYNC      当I/O可用的时候，允许SIGIO信号发送到进程组，例如：当有数据可以读的时候

```c
/*
1、获取文件的flags，即open函数的第二个参数:
       flags = fcntl(fd,F_GETFL,0);

2、设置文件的flags:
      fcntl(fd,F_SETFL,flags);

3、增加文件的某个flags，比如文件是阻塞的，想设置成非阻塞:
      flags = fcntl(fd,F_GETFL,0);
      flags |= O_NONBLOCK;
      fcntl(fd,F_SETFL,flags);

4、取消文件的某个flags，比如文件是非阻塞的，想设置成为阻塞:
      flags = fcntl(fd,F_GETFL,0);
      flags &= ~O_NONBLOCK;
      fcntl(fd,F_SETFL,flags);
 */

    int flags = ::fcntl(sockfd, F_GETFL, 0);
    flags |= O_NONBLOCK;
    int ret = ::fcntl(sockfd, F_SETFL, flags);

    // close-on-exec
    flags = ::fcntl(sockfd, F_GETFD, 0);
    flags |= FD_CLOEXEC;
    ret = ::fcntl(sockfd, F_SETFD, flags);
```



### 3. 演示程序

```c
#include <unistd.h>
#include <sys/types.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <errno.h>

#define err_sys(fmt, arg...) \
do { \
    printf(fmt, ##arg);\
    printf("\nerrno:%d %s\n", errno, strerror(errno));\
    exit(EXIT_FAILURE);\
} while (0)


int main()
{
    int fd, fd2, val;
    pid_t pid;
#ifdef _O_CLOEXEC
    if ((fd = open("test.txt", O_RDWR | O_CREAT | O_CLOEXEC, 0600)) < 0) 
#else
    if ((fd = open("test.txt", O_RDWR | O_CREAT, 0600)) < 0) 
#endif
        err_sys("open error");

#ifdef _DUP
    if ((fd2 = dup(fd)) < 0)
        err_sys("dup error");
    if (write(fd2, "123", 3) < 0)
        err_sys("write error");
#endif

#ifdef _FCNTL_CLOEXEC
    if ((val = fcntl(fd, F_GETFD)) < 0)
        err_sys("fcntl(F_GETFD) error");

    val |= FD_CLOEXEC;
    if (fcntl(fd, F_SETFD, val) < 0)
        err_sys("fcntl( F_SETFD) error");
#endif

#ifndef _FORK 
    if (execl("/bin/sleep", "sleep", "10000", (void*)0) < 0)
        err_sys("execl error");
#else
 switch ((pid = fork())) {
        case -1:
            err_sys("fork error");
        case 0:
            sleep(10000);
            break;
        default:
            sleep(10000);
            break;
    }
#endif

    return 0;
}
```

```shell
➜  Test gcc -D _O_CLOEXEC fcntl.c -o  app
➜  Test ./app 
#查看进程和进程资源，注意执行execl后进程名字变为sleep了
➜  ~ ps -aux |grep sleep
root     31749  0.0  0.0 107956   352 pts/0    S+   23:51   0:00 sleep 10000
➜  ~ lsof -p 31749      
COMMAND   PID USER   FD   TYPE DEVICE  SIZE/OFF    NODE NAME
sleep   31749 root  cwd    DIR  253,1      4096 1310757 /home/zbb/Test
sleep   31749 root  rtd    DIR  253,1      4096       2 /
sleep   31749 root  txt    REG  253,1     33216  659443 /usr/bin/sleep
sleep   31749 root  mem    REG  253,1 106075056  676514 /usr/lib/locale/locale-archive
sleep   31749 root  mem    REG  253,1   2156160  658656 /usr/lib64/libc-2.17.so
sleep   31749 root  mem    REG  253,1    163400  658371 /usr/lib64/ld-2.17.so
sleep   31749 root    0u   CHR  136,0       0t0       3 /dev/pts/0
sleep   31749 root    1u   CHR  136,0       0t0       3 /dev/pts/0
sleep   31749 root    2u   CHR  136,0       0t0       3 /dev/pts/0
# 可以看出进程资源中没有文件test.txt

#不加编译宏-D _O_CLOEXEC
➜  ~ ps -aux |grep sleep
root     32300  0.0  0.0 107956   356 pts/0    S+   23:59   0:00 sleep 10000
➜  ~ lsof -p 32300      
COMMAND   PID USER   FD   TYPE DEVICE  SIZE/OFF    NODE NAME
sleep   32300 root  cwd    DIR  253,1      4096 1310757 /home/zbb/Test
sleep   32300 root  rtd    DIR  253,1      4096       2 /
sleep   32300 root  txt    REG  253,1     33216  659443 /usr/bin/sleep
sleep   32300 root  mem    REG  253,1 106075056  676514 /usr/lib/locale/locale-archive
sleep   32300 root  mem    REG  253,1   2156160  658656 /usr/lib64/libc-2.17.so
sleep   32300 root  mem    REG  253,1    163400  658371 /usr/lib64/ld-2.17.so
sleep   32300 root    0u   CHR  136,0       0t0       3 /dev/pts/0
sleep   32300 root    1u   CHR  136,0       0t0       3 /dev/pts/0
sleep   32300 root    2u   CHR  136,0       0t0       3 /dev/pts/0
sleep   32300 root    3u   REG  253,1         0 1333907 /home/zbb/Test/test.txt
# 验证：调用open函数O_CLOEXEC模式打开的文件描述符在执行exec调用新程序中关闭，且为原子操作

➜  Test gcc -D _FCNTL_CLOEXEC  fcntl.c -o  app 
➜  Test ./app 
➜  ~ ps -aux|grep sleep
root       741  0.0  0.0 107956   356 pts/0    S+   00:13   0:00 sleep 10000
➜  ~ lsof -p  741
COMMAND PID USER   FD   TYPE DEVICE  SIZE/OFF    NODE NAME
sleep   741 root  cwd    DIR  253,1      4096 1310757 /home/zbb/Test
sleep   741 root  rtd    DIR  253,1      4096       2 /
sleep   741 root  txt    REG  253,1     33216  659443 /usr/bin/sleep
sleep   741 root  mem    REG  253,1 106075056  676514 /usr/lib/locale/locale-archive
sleep   741 root  mem    REG  253,1   2156160  658656 /usr/lib64/libc-2.17.so
sleep   741 root  mem    REG  253,1    163400  658371 /usr/lib64/ld-2.17.so
sleep   741 root    0u   CHR  136,0       0t0       3 /dev/pts/0
sleep   741 root    1u   CHR  136,0       0t0       3 /dev/pts/0
sleep   741 root    2u   CHR  136,0       0t0       3 /dev/pts/0

#调用open函数O_CLOEXEC模式打开的文件描述符，或是使用fcntl设置FD_CLOEXEC选项，这二者得到（处理）的描述符在通过fork调用产生的子进程中均不被关闭.
➜  Test gcc -D _FCNTL_CLOEXEC -D _FORK fcntl.c -o  app
➜  Test ./app
➜  ~ ps -aux |grep app
root      1041  0.0  0.0   4212   352 pts/0    S+   00:18   0:00 ./app
root      1042  0.0  0.0   4212    88 pts/0    S+   00:18   0:00 ./app
➜  ~ lsof -p 1041
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
app     1041 root  cwd    DIR  253,1     4096 1310757 /home/zbb/Test
app     1041 root  rtd    DIR  253,1     4096       2 /
app     1041 root  txt    REG  253,1     8560 1333906 /home/zbb/Test/app
app     1041 root  mem    REG  253,1  2156160  658656 /usr/lib64/libc-2.17.so
app     1041 root  mem    REG  253,1   163400  658371 /usr/lib64/ld-2.17.so
app     1041 root    0u   CHR  136,0      0t0       3 /dev/pts/0
app     1041 root    1u   CHR  136,0      0t0       3 /dev/pts/0
app     1041 root    2u   CHR  136,0      0t0       3 /dev/pts/0
app     1041 root    3u   REG  253,1        0 1333907 /home/zbb/Test/test.txt
➜  ~ lsof -p 1042
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
app     1042 root  cwd    DIR  253,1     4096 1310757 /home/zbb/Test
app     1042 root  rtd    DIR  253,1     4096       2 /
app     1042 root  txt    REG  253,1     8560 1333906 /home/zbb/Test/app
app     1042 root  mem    REG  253,1  2156160  658656 /usr/lib64/libc-2.17.so
app     1042 root  mem    REG  253,1   163400  658371 /usr/lib64/ld-2.17.so
app     1042 root    0u   CHR  136,0      0t0       3 /dev/pts/0
app     1042 root    1u   CHR  136,0      0t0       3 /dev/pts/0
app     1042 root    2u   CHR  136,0      0t0       3 /dev/pts/0
app     1042 root    3u   REG  253,1        0 1333907 /home/zbb/Test/test.txt

#调用dup族类函数得到的新文件描述符将清除O_CLOEXEC模式
➜  Test gcc -D _FCNTL_CLOEXEC -D _DUP fcntl.c -o  app
➜  Test ./app
➜  ~ lsof -p 1683
COMMAND  PID USER   FD   TYPE DEVICE  SIZE/OFF    NODE NAME
sleep   1683 root  cwd    DIR  253,1      4096 1310757 /home/zbb/Test
sleep   1683 root  rtd    DIR  253,1      4096       2 /
sleep   1683 root  txt    REG  253,1     33216  659443 /usr/bin/sleep
sleep   1683 root  mem    REG  253,1 106075056  676514 /usr/lib/locale/locale-archive
sleep   1683 root  mem    REG  253,1   2156160  658656 /usr/lib64/libc-2.17.so
sleep   1683 root  mem    REG  253,1    163400  658371 /usr/lib64/ld-2.17.so
sleep   1683 root    0u   CHR  136,0       0t0       3 /dev/pts/0
sleep   1683 root    1u   CHR  136,0       0t0       3 /dev/pts/0
sleep   1683 root    2u   CHR  136,0       0t0       3 /dev/pts/0
sleep   1683 root    4u   REG  253,1         3 1333907 /home/zbb/Test/test.txt

```
