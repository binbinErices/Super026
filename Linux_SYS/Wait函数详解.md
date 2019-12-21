# Wait函数详解

[TOC]

## kill

### 头文件

sys/types.h
signal.h

## 函数功能

注意此函数的功能是向指定进程发送信号。而不是杀死某个进程.名字为kill的原因是早期的Unix系统对信号的默认处理方式大部分是终止进程。

### 函数原型

int kill(pid_t pid, int sig);

返回值：执行成功返回0，执行失败返回-1。

### 参数说明

pid为进程ID,sig标识要发送的信号。

kill()函数的作用是用来向指定的进程或进程组发送信号。其中pid有一下
几种类型:

- pid > 0： 发送信号给**进程**标识为pid的进程
- pid = 0： 发送信号给当前进程**相同进程组**的所有进程
- pid = -1：发送信号给**系统内除了1号进程以外**的所有进程
- pid < -1：发送信号给**进程组**标识为-pid的进程。

**当sig=0时没有信号发出，但是系统会执行错误检查，通常会利用sig值为0来检查某个进程是否在执行。**

- 若进程不存在,errno为ESRCH
- 若errno为EPERM(无权向目标进程发送信号，但是存在)或调用成功，表示进程存在。

### Example

相关知识：WIFSIGNALED和WIFEXITED两个宏是用来判断当前程序的子进程的退出方式，是接收到信号异常退出
还是正常调用exit()或return退出。

- WIFEXITED: 调用exit()或return退出
- WIFSIGNALED:接收到信号异常退出 此时可以使用WTERMSIG(status)获取其接受信号的内容。

```
#include <unistd.h>
#include <signal.h>
#include <sys/types.h>
#include <sys/wait.h>
int main()
{
    pid_t pid;
    int status;
    if(!(pid= fork()))
    {
        printf("Hi I am child process!\n");
        sleep(10);
        return 0;
    }
    else
    {
        printf("send signal to child process (%d) \n", pid);
        sleep(1);
        kill(pid, SIGABRT);
        wait(&status);
        if(WIFSIGNALED(status))
            printf("chile process receive signal %d\n", WTERMSIG(status));
        return 0;
    }
}
```

## raise和killpg函数

除了kill函数可以向进程发送信号外,还可以通过raise和killpg函数发送信号。

## 函数原型

int raise(int sig);
int killpg(pid_t pgrpid, int sig);

## 函数功能

raise函数的功能是向进程自身发送信号.在单线程程序中，调用raise函数相当于调用kill(getpid(), sig).
对于支持线程的系统，一般将raise实现为:

```
pthread_kill(pthread_self(), sig);
```



此时信号只发送给当前进程的当前线程。而不影响其他线程。

killpg函数的作用是向同组的所有进程发送信号。相当于:

```
kill(-pgrpid, sig);
```



## 函数返回值

raise函数的错误返回值只有一个,即EINVAL，表示信号无效。其他时候返回都为成功。
而killpg的返回值与kill类似。

## wait()与waitpid()

### 头文件

sys/wait.h

### 函数原型

```
pid_t wait(int* status);
pid_t waitpid(pid_t pid, int* status, int options);
```

### 函数说明

父进程创建子进程之后，父进程使用wait和waitpid具有监视子进程的运行状态的能力。这两个函数用于**等待子进程的状态发生变化回调并且获取状态发生变化的信息**，所能获取的状态变化包括：**子进程运行结束,子进程被信号量暂停，子进程被信号量恢复运行**。

父进程执行wait函数之后，父进程会被阻塞在此处，如果子进程状态发生变化，则wait函数会立即返回结果；否则wait函数会一直阻塞直到子进程状态发生变化。

通常意义上，如果子进程状态发生了变化，但是还是未被其父进程或者其他系统回调执行wait函数，此时的子进程被称为可等待的。

子进程运行结束后父进程执行wait函数可以推动系统释放与子进程相关的资源；否则子进程将会被维持在**僵尸进程**（子进程已结束，而父进程还在运行）的状态下一直存在。

### 返回值说明

上述函数的返回值有-1,0,>0三种情况。分别对应于以下三种情况返回。

- -1:调用出错,此时出错信息在errno中
- 0:若waitpid的options设置了WNOHANG，且调用中没有子进程退出，立即返回0
- \>0:若大于0,返回退出进程的pid。

### 参数说明

- pid：要监听的进程的ID(<-1, =-1, =0, > 0)
- status: 用于存储出发状态变化时的信号值和exit(code)中的code值。
- options 提供一些额外的选项控制waitpid,目前linux中只支持WNOHANG和WUNTRACED两个选项，这是两个常数宏，可以使用|连接使用.

**pid详细说明**:

- pid < -1 : 监听范围为进程组为-pid的所有子进程
- pid = -1 : 监听范围为调用wait/waitpid的所有子进程
- pid = 0: 监听范围为子进程的进程组ID（与父进程相等）
- pid > 0: 监听特定pid的进程

**status详细说明**

status用于保存出发wait的信号值或者退出时exit(code)中的code值

**options详细说明**

- WNOHANG : 使用此参数调用waitpid,即使子进程没有退出，他也会立即返回，而不是像wait一直等下去
- WUNTRACED : 用于调试，极少用

一般情况下使用值为0即可。

### wait与waitpid关系

wait实质上是waitpid中pid=-1,options=0时封装，即

wait(&status)与waitpid(-1, &status, 0)完全相同

### 相关宏

wait.h中定义了一些宏用于解析status的值：

|         宏          | 含义                                    |
| :-----------------: | --------------------------------------- |
|  WIFEXITED(status)  | 子进程正常退出返回true否则false         |
| WEXITSTATUS(status) | 当正常退出时，返回exit(code)中的code    |
|     WIFSIGNALED     | 子进程接受信号退出时返回true，否则false |
|      WTERMSIG       | 被信号量杀死时，返回信号量的值          |
|  WIFSTOPED(status)  | 当子进程被信号量暂停时返回true          |
|  WSTOPSIG(status)   | 被信号量暂停时信号量的值                |

**options值**:

|    常量    | 含义                                                         |
| :--------: | ------------------------------------------------------------ |
|  WNOHANG   | 调用wait时制定pid仍未返回，wait立即返回0，用于判断子进程有没有结束 |
| WUNTRACED  | 当子进程被暂停时，则wait立即返回子进程的pid                  |
| WCONTINUED | 当被暂停的子进程又被信号量恢复后，则wait立即返回子进程的pid。Linux 2.6.10及以后生效。在Mac 0S X 10.9.5上未生效。 |