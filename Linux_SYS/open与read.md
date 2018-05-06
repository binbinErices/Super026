闲话少说，直接上代码：
```
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <errno.h>
#include <fcntl.h>

int main(void)
{
   int fd;
   fd = open("./1.txt",O_WRONLY|O_CREAT|O_APPEND,0777);

   if(0>fd)
   {
       perror("open file failure...");
       exit(-1);
   }

   char readdata[100];
   char writedata[100];

   strcpy(writedata,"hello world...\n");

   if(0>write(fd,writedata,strlen(writedata)+1))
   {
      perror("write file failure...");
      exit(-1);
   }


   close(fd);

   if(fd = open("./1.txt",O_RDONLY,0777)<0)
   {
      perror("read open file failure....");
      exit(-1);
   }
   
   printf("read open success..\n");

   if(0>read(fd,readdata,100))
   {
       perror("read file err...");
       exit(-1);
   }

   printf("recvdata:%s",readdata);

   close(fd);   
  
   return 0;
}

```
**函数功能：**
>代码逻辑很简单，就是open一个文件，往里面写一些数据，然后关闭文件描述符，把文件里面的数据读出，打印到屏幕上。

**调试过程：**
![1](https://img-blog.csdn.net/20180505162413518?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VyaWNlX3M=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![2](https://img-blog.csdn.net/20180505162422896?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VyaWNlX3M=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**功能变成了等待终端输入数据然后回显。**

*调试一会，审核代码的逻辑什么的都没有错误*
*but...*

**想到了linux文件描述符：**

|文件描述符|缩写|描述|
|----|---|----|
|0|STDIN|标准输入|
|1|STDOUT|标准输出|
|2|STDERR|标准错误输出|

## 关键点，一定是文件描述符fd变成了0
在review代码，找到错误：

```
  if(fd = open("./1.txt",O_RDONLY,0777)<0)
   {
      perror("read open file failure....");
      exit(-1);
   }
  /* 
   * open("./1.txt",O_RDONLY,0777)调用成功时返回一个文件描述符fd，调用失败时返回-1，并修改errno
   * 成功时fd>0; open("./1.txt",O_RDONLY,0777)<0 不成立 返回 false（0）
   * 于是fd就等于0!
   * 将if判断条件修改为if((fd = open("./1.txt",O_RDONLY,0777))<0)，调试成功！
   */
 
```
# 反思：Coding时一定要细心，细心，再细心 ...

[**关于Linux文件权限请戳。。。**](https://blog.csdn.net/Erice_s/article/details/80206839)