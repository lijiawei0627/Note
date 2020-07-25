# 一、shell语法
命令&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 选项&nbsp; &nbsp; &nbsp; 参数

[root@iZ2ze0wa1jfjm9k6xknj3vZ /]# ls

[root@iZ2ze0wa1jfjm9k6xknj3vZ /]# ls -l

[root@iZ2ze0wa1jfjm9k6xknj3vZ /]# ls -l /

* 命令：整条shell命令的主体

* 选项：会影响会微调命令的行为   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//通常以-，--开头

* 参数：命令作用的对象

# 二、常用命令
## cat

查看文件内容

> head命令用于获取一个文件的前10行内容。tail命令用于获取一个文本文件的后10行内容。grep命令用于过滤搜索 如：grep 'root' /etc/passwd

## ls

目录

## mkdir 

创建目录命令

## tree 

以树状图列出目录的内容

## mv 

移动文件或文件目录命令

### mv的应用

``` shell
    [root@localhost linuxidc]# ls
    a.py  a.sh
    [root@localhost linuxidc]# mv a.py b.py      #只是改名了
    [root@localhost linuxidc]# ls
    a.sh  b.py
    
    [root@localhost linuxidc]# ls /root/a/
    1.py  2.py  3.py  4.py  5.py
    [root@localhost linuxidc]# pwd
    /home/linuxidc
    [root@localhost linuxidc]# mv /root/a/* .    #移动a目录下所有文件到当前目录(*代表的是通配       符，.代表的是当前目录)
    [root@localhost linuxidc]# ls
    1.py  2.py  3.py  4.py  5.py  a.sh  b.py
```

## mkdir 

创建目录命令

## cp 

复制命令

```shell
[root@localhost ~]# cp a.py a/  //复制当前目录下的a.py文件到a目录下

[root@localhost ~]# cp a.py b.py
//把文件复制到当前文件夹，并改名
```

>  **注意，同mv命令一样，肯定是会用到相对文件路径的，" . "代表当前文件路径，".."代表上一层文件路径，" ~ "代表家目录**

## touch 

创建文件和修改文件或者目录的时间戳

例如：
``` shell
   //同时新建多个文件
   touch /home/dir10/{file1, file2, file3}
```

## rm  

### 功能

* 删除目录
* 删除文件

### 命令选项

```shell
-f：不提示，强制删除文件或目录；
-i：删除已有文件或目录之前先询问用户；
-r,-R：递归删除，将指定目录下的所有文件与子目录一并删除；
-v：显示指令的详细执行过程
```

## history

（#后面为注释）   用于显示历史记录和执行过的指令命令

```shell
  1、选项:

    -N: 显示历史记录中最近的N个记录；
    -c：清空当前历史命令；

  2、参数:

    n：打印最近的n条历史命令。
    
  3、使用! 执行历史命令。
  [linuxidc@linuxidc ~]$ !10 #执行历史记录第 10 条命令
  [linuxidc@linuxidc ~]$ !! #执行上一条命令
  
```
## 设置主机名

```shell
[linuxidc@linuxidc ~]$ hostname #查看当前系统主机名，知道当前主机名为localhost
[linuxidc@linuxidc ~]$ hostnamectl set-hostname lijiawei
[linuxidc@linuxidc ~]$ reboot #重启
```

# 三、快捷键
^C  终止前台运行的程序  

^D  退出 等价exit

^L  清屏

^A  光标移动到命令行的最前端

^E  光标移动到命令行的最后端

^U  剪切（删除）光标前所有字符

^K  剪切（删除）光标后所有字符

^Y  粘贴刚才所删除的字符

^R  搜索历史命令，利用关键词

^/  类似于windows的^Z

^C  删除整行(换一行进行重新输入)

# 四、 tab键补齐

 tab键补齐原则（唯一补齐），当连续按下两次tab键，会出来所有的补齐结果

# 五、yum

yum是一个在Fedora和RedHat以及SUSE中的Shell前端软件包管理器。

## yum语法

```shell
yum [options] [command] [package ...]

options：可选，选项包括-h（帮助），-y（当安装过程提示选择全部为"yes"），-q（不显示安装的过程）等等。
command：要进行的操作。
package 操作的对象。
```

## yum常用命令

* 列出所有可更新的软件清单命令：

  ```shell
  [linuxidc@linuxidc ~]$ yum check-update
  ```

* 更新所有软件命令：

  ```shell
  [linuxidc@linuxidc ~]$ yum update  #升级所有包同时也升级软件和系统内核
  
  [linuxidc@linuxidc ~]$ yum -y upgrade #只升级所有包，不升级软件和系统内核
  ```

* 仅安装指定的软件命令：

  ```shell
  [linuxidc@linuxidc ~]$ yum install <package_name>
  ```

* 仅更新指定的软件命令：

  ```shell
  [linuxidc@linuxidc ~]$ yum update <package_name>
  ```

* 列出所有可安裝的软件清单命令：

  ```shell
  [linuxidc@linuxidc ~]$ yum list
  ```

* 删除软件包命令：

  ```shell
  [linuxidc@linuxidc ~]$ yum remove <package_name>
  ```

* 查找软件包 命令：

  ```shell
  [linuxidc@linuxidc ~]$ yum search <keyword>
  ```

* 清除缓存命令:

  ```shell
  [linuxidc@linuxidc ~]$ yum clean packages # 清除缓存目录下的软件包
  [linuxidc@linuxidc ~]$ yum clean headers  # 清除缓存目录下的 headers
  [linuxidc@linuxidc ~]$ yum clean oldheaders # 清除缓存目录下旧的 headers
  [linuxidc@linuxidc ~]$ yum clean, yum clean all (= yum clean packages; yum clean oldheaders) # 清除缓存目录下的软件包及旧的headers
  ```


# 六、man命令（联机手册）

## man的级别

* **1    用户命令**

* 2    系统接口

* 3    函数库

* 4    特殊文件，比如设备文件

* **5    文件格式**

* 6    游戏

* 7    系统的软件包

* **8    系统管理命令**

* 9    内核

## man手册的使用方法

   例如：man ls  后

　查看时需要翻屏：

        向后翻一屏：space(空格键)    向前翻一屏：b
    
        向后翻一行：Enter(回车键)    　　 向前翻一行：k

   查看时需要查找：

    /关键词      向后查找    n：下一个
    
    ?关键词     向前查找    N：前一个

# 七、Linux目录结构
[链接](https://www.jianshu.com/p/c6ffbfaa6e97)

# 八、Linux文件路径
```

.  代表当前工作目录;  

..   代表当前工作内目录的上一级目录

-    代表前一个工作目录

~  代表当前用户的家目录（root用户家目录为/root/目录，普通用户家目录为/home/目录）
```

# 九、vim

## 模式

**vim有3种常用模式：一般模式、编辑模式、命令模式。**

### 一般模式

```bash
上下左右:kjhl 也可以使用方向键

n+上下左右:n为数字，向上下左右移动n个字符

[ctrl]+f:屏幕向下滚动一页，同page down

[ctrl]+b:屏幕向上滚动一页，同page up

0或home:光标移动到行首

$或end:光标移动到行尾

g:光标移动到最后一行

ng:光标移动到第n行

gg:同1g，光标移动到第一行行首

/word:向下查找关键词，使用n或N向上或向下查找关键词

?word:向上查找关键词word，使用n或N向上或向下查找关键词

:n1,n2s/word1/word2/g  :  s/1/2/g 表示将1替换成2，所以前面的意思是在n1到n2之间，将word1替换为word2.例如：51,100s/aaa/bbb/g

:1,$s/word1/word2/g : 全文查找替换将word1替换为word2

x:向后删除

X:向前删除

nx:向后删除n个字符

dd:删除当前行　

ndd:向下删除n行

d1G:删除当前位置到第一行

d$:删除当前位置到最后一行

d0:删除当前位置到改行第一个字符的所有数据

yy:复制光标所在的这一行

nyy:向下复制n行

p:在光标所在行的下面粘贴复制的数据

P:在光标所在行的上面粘贴复制的数据

u:恢复前一个操作

[ctrl]+u:重做上一个操作
```

### 一般模式切换到编辑模式

```bash
i:进入插入模式，在光标前插入  I是在第一个非空格符处插入

a:进入插入模式，在光标下一个字符插入 A是在所在行最后一个字符插入

o:进入插入模式，在下面一行插入  O是在上面一行出入

r:进入替换模式，类似于insert键
```

### 一般模式到命令模式

```bash
:w 保存

:w! 强制保存

:q 退出

:q! 强制退出

:wq :x 保存并退出

ZZ 保存并退出

:set number 显示行号

:set nonumber 取消显示行号
```

## 多文件编辑

`vim file1 file2`可以同时打开两个文件

```bash
:n 编辑下一个文件

:N 编辑上一个文件

:file 列出这个vim打开的所有文件
```

