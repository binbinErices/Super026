# Linux文件权限
## 目录
[TOC]

## 1.Linux文件权限
每个Linux文件具有四种访问权限：**可读(r)、可写(w)、可执行(x)和无权限(-)**
利用ls -l命令可以看到某个文件或目录的权限，它以显示数据的第一个字段为准。第一个字段由10个字符组成，如下：

```
zbb@python:~/Cpro/ttms-master$ ls -l
总用量 136
drwxrwxr-x 2 zbb zbb   4096 5月   5 12:44 control
-rw-rw-r-- 1 zbb zbb    346 10月  3  2015 Makefile
drwxrwxr-x 2 zbb zbb   4096 5月   5 12:44 model
-rwxrwxr-x 1 zbb zbb 106224 5月   5 12:44 ms
-rw-rw-r-- 1 zbb zbb     35 10月  3  2015 README.md
drwxrwxr-x 2 zbb zbb   4096 5月   5 12:44 ttms
-rw-rw-r-- 1 zbb zbb   2072 5月   5 12:50 ttms.dat
drwxrwxr-x 2 zbb zbb   4096 5月   5 12:44 util
drwxrwxr-x 2 zbb zbb   4096 5月   5 12:44 view

```

- 第1位表示文件类型，-表示文件，d表示目录
- 2-4位：　　表示文件所有者的权限，u权限
- 5-7位：　　表示文件所有者所属组成员的权限，g权限
- 8-10位：　 表示所有者所属组之外的用户的权限，o权限   
- 2-10位：　 表示的权限总和有时称为a权限

```
drwxrwxr-x 2 zbb zbb   4096 5月   5 12:44 control
```
在上面的例子中，表示这是一个目录(非文件），文件所有者具有读、写和执行的权限，所有者所属组成员具有读、写和执行的权限，所属组之外的用户具有读和执行的权限而没有写的权限。

## 2. 文件的权限修改

### 用数字表示法修改权限
>数字表示法：是指将r、w和x分别用4、2、1来代表，没有授予权限的则为0，然后把权限相加。

| 原始权限 | 转换为数字 | 数字表示法 |
|-----|:--------:|------:|
|rwxrwxr-x|(421)(421)(401)|775|
|rw-rw-r--|(420)(420)(400)|664|


**修改权限的实例**：将文件test的权限修改为所有者具有读写执行权限，组成员具有读执行权限，其他人具有执行的权限。

**修改方法：chmod 751 test**
```
zbb@python:~/Cpro/dir$ ls -l
总用量 12
-rwxrwxr-x 1 zbb zbb 9072 5月   5 15:56 test
zbb@python:~/Cpro/dir$ chmod 751 test
zbb@python:~/Cpro/dir$ ls -l
总用量 12
-rwxr-x--x 1 zbb zbb 9072 5月   5 15:56 test

```

### 用文本表示法修改权限

文本表示法用4个字母表示不同的用户，读、写、执行权限仍用r、w和x表示：
1. u：所有者
2. g：组成员
3. o：其他成员
4. a：所有人
　　
跟数字表示法相比，文本表示法不仅可以重新指定权限，也可以在原来权限的基础上增加或减少权限，如下：

=：重新制定权限
-：对目前的设置减少权限
+：对目前的设置增加权限

例子：讲上述例子中，所有者减少执行权限，组成员增加写权限，其他成员设置为读写权限，执行以下命令：chmod u-x,g-x,o+rw test
**注意：逗号前后不能有空格**

```
zbb@python:~/Cpro/dir$ ls -l
总用量 12
-rwxr-x--x 1 zbb zbb 9072 5月   5 15:56 test
zbb@python:~/Cpro/dir$ chmod u-x,g-x,o+rw test
zbb@python:~/Cpro/dir$ ls -l
总用量 12
-rw-r--rwx 1 zbb zbb 9072 5月   5 15:56 test
```

## 3. 目录权限

目录权限的修改和文件权限修改不同，只是四种权限代表的含义如下：
r：可列出目录中的内容
w：可在目录中创建、删除和修改文件
x：可以使用cd命令切换到此目录
-：没有任何此目录的访问权限
注意：目录可以使用通配符"*"来表示目录中的所有文件，如将/dir目录中的所有文件的权限设置为任何人都可以读写

chmod 666 /dir/*

## 4. 指定文件的默认权限掩码-----umask

权限掩码有4个八进制的数字组成，讲现有的权限减掉权限掩码后，即可产生此文件建立时的默认权限。
一般来说，新建文件的默认值是0666，新建目录的默认值是0777，如果将全线掩码设置为0002，则每个新建文件的默认权限为0666-0002=0664，而目录的默认权限则为775。
可以直接输入umask命令来检查目前的默认权限掩码，或输入"umask 权限掩码"来指定默认权限掩码。
用umask的方式指定默认权限掩码，可以避免添加访问权限过大的文件或目录。

```
zbb@python:~/Cpro$ umask 0004
zbb@python:~/Cpro$ mkdir dir1
zbb@python:~/Cpro$ umask 0001
zbb@python:~/Cpro$ mkdir dir2
zbb@python:~/Cpro$ umask 0002
zbb@python:~/Cpro$ mkdir dir3
zbb@python:~/Cpro$ ls -l
drwxrwx-wx 2 zbb zbb  4096 5月   5 16:11 dir1
drwxrwxrw- 2 zbb zbb  4096 5月   5 16:11 dir2
drwxrwxr-x 2 zbb zbb  4096 5月   5 16:12 dir3

```
