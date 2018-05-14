# Linux struct stat介绍

[TOC]

在使用这个结构体和方法时，需要引入：
```
<sys/types.h>
<sys/stat.h>
```
struct stat这个结构体是用来描述一个linux系统文件系统中的文件属性的结构。

可以有两种方法来获取一个文件的属性：

## 1. stat 与 lstat函数

```
int stat(const char *path, struct stat *struct_stat);
int lstat(const char *path,struct stat *struct_stat);
```

两个函数的第一个参数都是文件的路径，第二个参数是struct stat的指针。
成功返回0，失败会返回-1并且设置errno
**ERRORS:**
```
EACCES Search permission is denied for one of the directories in the path prefix of pathname. 
 (See also path_resolution(7).)
 [对于路径名的路径前缀中的其中一个目录，EACCES搜索权限被拒绝。（另请参阅path_resolution（7）。）]

       EBADF  fd is bad. 文件描述词无效

       EFAULT Bad address. 地址空间不可访问

       ELOOP  Too many symbolic links encountered while traversing the path.
       遍历路径时遇到太多的符号连接

       ENAMETOOLONG pathname is too long.  文件路径名太长

       ENOENT A component of pathname does not exist, or pathname is an empty string.
       路径名的部分组件不存在，或路径名是空字串

       ENOMEM Out of memory (i.e., kernel memory).   内存不足

       ENOTDIR  A component of the path prefix of pathname is not a directory.
       路径名的部分组件不是目录

       EOVERFLOW
              pathname  or fd refers to a file whose size, inode number, or number of blocks cannot be represented in, respectively, the types off_t, ino_t, or blkcnt_t.  
       路径名或fd是指文件的大小，inode编号或块数不能分别表示类型off_t，ino_t或blkcnt_t。
 
       The following additional errors can occur for fstatat():

       EBADF  dirfd is not a valid file descriptor.  dirfd不是有效的文件描述符。
       EINVAL Invalid flag specified in flags. 标志中指定的标志无效。
       ENOTDIR    pathname is relative and dirfd is a file descriptor referring to a file other than a directory.  路径名是相对的，dirfd是引用除目录以外的文件的文件描述符。

```

这两个方法区别在于stat没有处理字符链接(软链接）的能力，如果一个文件是符号链接，stat会直接返回它所指向的文件的属性；而lstat返回的就是这个符号链接的内容。这里需要说明一下的是软链接和硬链接的含义。我们知道目录在linux中也是一个文件，文件的内容就是这这个目录下面所有文件与inode的对应关系。那么所谓的硬链接就是在某一个目录下面将一个文件名与一个inode关联起来，其实就是添加一条记录！而软链接也叫符号链接更加简单了，这个文件的内容就是一个字符串，这个字符串就是它所链接的文件的绝对或者相对地址。

## 2. ln命令
ln命令用来为文件创件连接，连接类型分为硬连接和符号连接(软链接)两种，默认的连接类型是硬连接。如果要创建符号连接必须使用"-s"选项。
注意：符号链接文件不是一个独立的文件，它的许多属性依赖于源文件，所以给符号链接文件设置存取权限是没有意义的

**语法：**

```
ln(选项)(参数)
选项
-b或--backup：删除，覆盖目标文件之前的备份；
-d或-F或——directory：建立目录的硬连接；
-f或——force：强行建立文件或目录的连接，不论文件或目录是否存在；
-i或——interactive：覆盖既有文件之前先询问用户；
-n或--no-dereference：把符号连接的目的目录视为一般文件；
-s或——symbolic：对源文件建立符号连接，而非硬连接；
-S<字尾备份字符串>或--suffix=<字尾备份字符串>：用"-b"参数备份目标文件后，备份文件的字尾会被加上一个备份字符串，预设的备份字符串是符号“~”，用户可通过“-S”参数来改变它；
-v或——verbose：显示指令执行过程；
-V<备份方式>或--version-control=<备份方式>：用“-b”参数备份目标文件后，备份文件的字尾会被加上一个备份字符串，这个字符串不仅可用“-S”参数变更，当使用“-V”参数<备份方式>指定不同备份方式时，也会产生不同字尾的备份字符串；
--help：在线帮助；
--version：显示版本信息。
参数
源文件：指定连接的源文件。【源文件是已经创建存在的文件】，如果使用-s选项创建符号连接，则“源文件”可以是文件或者目录。创建硬连接时，则“源文件”参数只能是文件；
目标文件：指定源文件的目标连接文件，【不存在的文件】
```

## 3. fstat函数

int fstat(int fdp, struct stat *struct_stat);　　//**通过文件描述符获取文件对应的属性。fdp为文件描述符**

 
下面是这个结构的结构
```
struct stat {

        mode_t     st_mode;       //文件对应的模式，文件，目录等
        ino_t      st_ino;       //inode节点号
        dev_t      st_dev;        //设备号码
        dev_t      st_rdev;       //特殊设备号码
        nlink_t    st_nlink;      //文件的连接数
        uid_t      st_uid;        //文件所有者
        gid_t      st_gid;        //文件所有者对应的组
        off_t      st_size;       //普通文件，对应的文件字节数
        time_t     st_atime;      //文件最后被访问的时间
        time_t     st_mtime;      //文件内容最后被修改的时间
        time_t     st_ctime;      //文件状态改变时间
        blksize_t st_blksize;    //文件内容对应的块大小
        blkcnt_t   st_blocks;     //伟建内容对应的块数量
      };
```

```
stat指令：文件/文件系统的详细信息显示.
stat命令主要用于显示文件或文件系统的详细信息
-f　　不显示文件本身的信息，显示文件所在文件系统的信息 
-Z 打印SElinux安全上下文信息 
-L　 显示符号链接 
-c 自定义输出格式，结尾有换行 
-t　 简洁模式，只显示摘要信息
root@python:~# stat .profile
  File: '.profile'
  Size: 148       	Blocks: 8          IO Block: 4096   regular file
Device: fd01h/64769d	Inode: 393220      Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2018-05-14 17:09:59.576326398 +0800
Modify: 2018-05-14 17:09:59.576326398 +0800
Change: 2018-05-14 17:09:59.576326398 +0800
 Birth: -

root@python:~# stat -f .profile
  File: ".profile"
    ID: 21f2a64b2ac66d08 Namelen: 255     Type: ext2/ext3
Block size: 4096       Fundamental block size: 4096
Blocks: Total: 10287952   Free: 9403076    Available: 8874719
Inodes: Total: 2621440    Free: 2500762

```

stat结构体中的st_mode 则定义了下列数种情况：

    S_IFMT   0170000    文件类型的位遮罩
    S_IFSOCK 0140000    scoket
    S_IFLNK 0120000     符号连接
    S_IFREG 0100000     一般文件
    S_IFBLK 0060000     区块装置
    S_IFDIR 0040000     目录
    S_IFCHR 0020000     字符装置
    S_IFIFO 0010000     先进先出

    S_ISUID 04000     文件的(set user-id on execution)位
    S_ISGID 02000     文件的(set group-id on execution)位
    S_ISVTX 01000     文件的sticky位

    S_IRUSR(S_IREAD) 00400     文件所有者具可读取权限
    S_IWUSR(S_IWRITE)00200     文件所有者具可写入权限
    S_IXUSR(S_IEXEC) 00100     文件所有者具可执行权限

    S_IRGRP 00040             用户组具可读取权限
    S_IWGRP 00020             用户组具可写入权限
    S_IXGRP 00010             用户组具可执行权限

    S_IROTH 00004             其他用户具可读取权限
    S_IWOTH 00002             其他用户具可写入权限
    S_IXOTH 00001             其他用户具可执行权限

    上述的文件类型在POSIX中定义了检查这些类型的宏定义：
    S_ISLNK (st_mode)    判断是否为符号连接
    S_ISREG (st_mode)    是否为一般文件
    S_ISDIR (st_mode)    是否为目录
    S_ISCHR (st_mode)    是否为字符装置文件
    S_ISBLK (s3e)        是否为先进先出
    S_ISSOCK (st_mode)   是否为socket
    
若一目录具有sticky位(S_ISVTX)，则表示在此目录下的文件只能被该文件所有者、此目录所有者或root来删除或改名，在linux中，最典型的就是这个/tmp目录啦。