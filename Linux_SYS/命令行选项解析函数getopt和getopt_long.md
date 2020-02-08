# 命令行选项解析函数: getopt和getopt_long

[TOC]

## 0x00 函数出处

```c
#include <unistd.h>
int getopt(int argc, char * const argv[],
const char *optstring);

extern char *optarg;
extern int optind, opterr, optopt;

#include <getopt.h>
int getopt_long(int argc, char * const argv[],
const char *optstring,
const struct option *longopts, int *longindex);

int getopt_long_only(int argc, char * const argv[],
const char *optstring,
const struct option *longopts, int *longindex);

```

## 0x01 getopt函数

### 1. 函数签名

```c
#include <unistd.h>
int getopt(int argc, char * const argv[],
const char *optstring);

extern char *optarg;
extern int optind, opterr, optopt;
```

### 2. 函数说明

```c
getopt是用来解析命令行选项参数的，但是只能解析短选项: -d 100,不能解析长选项：--prefix
argc：main()函数传递过来的参数的个数
argv：main()函数传递过来的参数的字符串指针数组
optstring：选项字符串，告知 getopt()可以处理哪个选项以及哪个选项需要参数
optarg —— 指向当前选项参数(如果有)的指针。
optind: 变量optind是argv中要处理的下一个元素的索引。系统将该值初始化为1。调用方可以将其重置为1，以重新开始扫描相同的argv，或者在扫描新的自变量向量时重新启动
optopt —— 最后一个未知选项。
opterr —— 如果不希望getopt()打印出错信息，则只要将全域变量opterr设为0即可。
```

```c
   如果getopt()找到另一个选项字符，它将返回该字符，更新外部变量optind和静态变量nextchar，以便对getopt()的下一次调用可以使用以下选项字符或argv元素恢复扫描。
   如果没有更多的选项字符，则getopt（）返回-1。然后optind是第一个argv元素的argv中的索引，该索引不是选项。
   optstring是包含合法选项字符的字符串。如果此类字符后接冒号，则此选项需要一个参数，因此getopt（）将指针指向位于同一argv-element中的后续文本，或位于optarg中的后续argv-element的文本。两个冒号表示一个选项带有一个可选的arg；如果当前argv元素中有文本（即与选项名称本身相同的词，例如“ -oarg”），则将其以optarg返回，否则optarg设置为零。这是一个GNU扩展。如果optstring包含W后跟一个分号，则-W foo被视为long选项--foo。 （-W选项由POSIX.2保留用于实现扩展。）
   此行为是GNU扩展，不适用于glibc2之前的库
```
### 3. 函数返回值

如果选项成功找到，返回选项字母；如果所有命令行选项都解析完毕，返回 -1；如果遇到选项字符不在 optstring 中，返回字符 '?'；如果遇到丢失参数，那么返回值依赖于 optstring 中第一个字符，如果第一个字符是 ':' 则返回':'，否则返回'?'并提示出错误信息。

### 4. optstring的格式意义

```
char*optstring = “ab:c::”;
单个字符a         表示选项a没有参数            格式：-a即可，不加参数
单字符加冒号b:     表示选项b有且必须加参数      格式：-b 100或-b100,但-b=100错
单字符加2冒号c::   表示选项c可以有，也可以无     格式：-c200，其它格式错误
```

上面这个 optstring 在传入之后，getopt 函数将依次检查命令行是否指定了 -a， -b， -c(这需要多次调用 getopt 函数，直到其返回-1)，当检查到上面某一个参数被指定时，函数会返回被指定的参数名称(即该字母)



### 5. 实例

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/types.h>
 
int main(int argc, char *argv[])
{   
        extern char *optarg;
        extern int optind, opterr, optopt;
        int i;
        int ret;
 
        for (i=0; i<argc; i++)
        {   
                printf ("argv[%d] %s\n", i, argv[i]);
        }   
        printf ("\n");
 
        while ((ret = getopt(argc, argv, ":a:b::c")) != -1)
        {   
                switch (ret) {
                case 'a':
                        printf ("option: %c argv: %s\n", ret, optarg);
                        break;
                case 'b':
                        if (optarg)
                                printf ("option: %c argv: %s\n", ret, optarg);
                        else
                                printf ("option: %c no argument\n", ret);
                        break;
                case 'c':
                        printf("option: %c argv: %s\n", ret, optarg);
                        break;
                case '?':
                        printf ("encountered a unrecognized option: %c, argv: %s\n", optopt, argv[optind - 1]);
                        break;
                case ':':
                        printf ("option: %c missing argument\n", optopt);
                        break;
                default:
                        printf ("option: %c\n", ret);
                        break;
                }   
        }
 
        printf ("\noptind: %d\n", optind);
        printf ("argc: %d\n", argc);
        
        for (i=optind; i>0 && i<argc; i++)
                printf ("argv[%d] %s\n", i, argv[i]);
 
        printf ("\n");
        for (i=0; i<argc; i++)
                printf ("argv[%d] %s\n", i, argv[i]);
 
        return 0;
}
```

**执行结果：**

```sh
>➜  Test ./a.out -a
argv[0] ./a.out
argv[1] -a

option: a missing argument

optind: 2
argc: 2

argv[0] ./a.out
argv[1] -a
➜  Test vim getopt.c #while ((ret = getopt(argc, argv, "a:b::c")) != -1)
#如果遇到选项字符不在 optstring 中，返回字符 '?'；如果遇到丢失参数，那么返回值依赖于 optstring 中第一个字符，如果第一个字符是 ':' 则返回':'，否则返回'?'并提示出错误信息
➜  Test gcc getopt.c 
➜  Test ./a.out -a
argv[0] ./a.out
argv[1] -a

./a.out: option requires an argument -- 'a'
encountered a unrecognized option: a, argv: -a

optind: 2
argc: 2

argv[0] ./a.out
argv[1] -a
```

```c
#include<stdio.h>
#include<unistd.h>
#include<getopt.h>
int main(intargc, char *argv[])
{
    int opt;
    char *string = "a::b:c:d";
    while ((opt = getopt(argc, argv, string))!= -1)
    {  
        printf("opt = %c\t\t", opt);
        printf("optarg = %s\t\t",optarg);
        printf("optind = %d\t\t",optind);
        printf("argv[optind] = %s\n",argv[optind]);
    }  
}
```

## 0x02 getopt_long函数 

### 1. 函数签名

```c
#include <getopt.h>
int getopt_long(int argc, char * const argv[],
const char *optstring,
const struct option *longopts, int *longindex);
```

### 2. 函数功能

```
包含 getopt 功能，增加了解析长选项的功能如：--prefix --help
```

### 3. 函数参数

```c
longopts    指明了长参数的名称和属性struct option
struct option {
const char  *name;       /* 参数名称 */
int          has_arg;    /* 指明是否带有参数 */
int          *flag;      /* flag=NULL时,返回value;不为空时,*flag=val,返回0 */
int          val;        /* 用于指定函数找到选项的返回值或flag非空时指定*flag的值 */
}; 
has_arg  指明是否带参数值，其数值可选：
 no_argument         表明长选项不带参数，如：--name, --help
 required_argument  表明长选项必须带参数，如：--prefix /root或 --prefix=/root
 optional_argument  表明长选项的参数是可选的，如：--help或 –prefix=/root，其它都是错误
    
longindex   如果longindex非空，它指向的变量将记录当前找到参数符合longopts里的第几个元素的描述，即是longopts的下标值

```

### 4. 函数返回值

```c
对于短选项，返回值同getopt函数；对于长选项，如果flag是NULL，返回val，否则返回0；对于错误情况返回值同getopt函数
```



### 5. 实例

```c
#include <unistd.h>
#include <stdio.h>
#include <getopt.h>

int main(int argc, char *argv[])
{
    int opt;
    int digit_optind = 0;
    int option_index = 0;
    char *string = "a::b:c:d";
    static struct option long_options[] =
    {  
        {"reqarg", required_argument,NULL, 'r'},
        {"optarg", optional_argument,NULL, 'o'},
        {"noarg",  no_argument,      NULL,'n'},
        {NULL,     0,                NULL, 0},
    }; 
    while((opt =getopt_long(argc,argv,string,long_options,&option_index))!= -1)
    {  
        printf("opt = %c\t\t", opt);
        printf("optarg = %s\t\t",optarg);
        printf("optind = %d\t\t",optind);
        printf("argv[optind] =%s\t\t", argv[optind]);
        printf("option_index = %d\n",option_index);
    }  
}
```

**执行结果：**

```sh
#正确输入长选项的情况 required_argument  表明长选项必须带参数，如：--prefix /root或 --prefix=/root
➜  Test ./optlong --reqarg=/home/root/bin --optarg=200 --noarg
opt = r		optarg = /home/root/bin		optind = 2		argv[optind] =--optarg=200		option_index = 0
opt = o		optarg = 200		optind = 3		argv[optind] =--noarg		option_index = 1
opt = n		optarg = (null)		optind = 4		argv[optind] =(null)		option_index = 2

➜  Test ./optlong --reqarg /home/root/bin --optarg=200 --noarg
opt = r		optarg = /home/root/bin		optind = 3		argv[optind] =--optarg=200		option_index = 0
opt = o		optarg = 200		optind = 4		argv[optind] =--noarg		option_index = 1
opt = n		optarg = (null)		optind = 5		argv[optind] =(null)		option_index = 2

#optional_argument表明长选项的参数是可选的，如：--help或 –prefix=/root，其它都是错误，--optarg 200 ERR
➜  Test ./optlong --reqarg /home/root/bin --optarg 200 --noarg
opt = r		optarg = /home/root/bin		optind = 3		argv[optind] =--optarg		option_index = 0
opt = o		optarg = (null)		optind = 4		argv[optind] =200		option_index = 1
opt = n		optarg = (null)		optind = 6		argv[optind] =(null)		option_index = 2

#可选长选项可以缺省
➜  Test ./optlong --reqarg /home/root/bin --optarg  --noarg   
opt = r		optarg = /home/root/bin		optind = 3		argv[optind] =--optarg		option_index = 0
opt = o		optarg = (null)		optind = 4		argv[optind] =--noarg		option_index = 1
opt = n		optarg = (null)		optind = 5		argv[optind] =(null)		option_index = 2

#长选项必须带选项，否则解析出错。 --optarg=200没有解析出来
➜  Test ./optlong --reqarg  --optarg=200  --noarg
opt = r		optarg = --optarg=200		optind = 3		argv[optind] =--noarg		option_index = 0
opt = n		optarg = (null)		optind = 4		argv[optind] =(null)		option_index = 2

```

## 0x03 getopt_long_only函数

getopt_long_only 函数与 getopt_long 函数使用相同的参数表，在功能上基本一致。

只是 getopt_long 只将 --name 当作长参数
getopt_long_only 会将 --name 和 -name 两种选项都当作长参数来匹配。
getopt_long_only 如果选项 -name 不能在 longopts 中匹配，但能匹配一个短选项，它就会解析为短选项。