# Linux加强

## 一、课程介绍

- 基础命令复习
- 加强命令(locate、ifconfig、ln、chmod、apt、vcpkg、df)
- 目录与文件相关命令
- 进程的相关命令
- 开机自启动

## 二、基础命令复习

### 1、pwd

### 2、cd

### 3、tab

### 4、ls/ll=ls -al

### 5、find

```bash
find / -name "code"
```

### 6、whereis

### 7、su

## 三、加强命令

### 1、locate

从索引库中快速查找文件或目录，默认情况下 updatedb 每天执行一次。也可以 手工更新索引库

```shell
sudo updatedb
```

### 2、ifconfig

显示网络信息

### 3、ln

#### 3.1、运用

添加-s创建软连接，相当于创建文件或目录的快捷方式，源文件被删除则无法再访问

```shel
ln -s  /home/lee/文档/cpp mycpp
```

不添加-s则创建硬连接，只能连接存在的文件，不能连接目录

### 4、chmod

只保留只读权限

```
chmod 444 1.txt
```

添加所有权限

```
chmod -R a+rwx ./*

chmod -R 777 ./*
```

### 5、apt

```
sudo apt-get check
# 检查是否有损坏的依赖

sudo apt-get update		      
# 更新源

sudo apt-get upgrade
# 升级已安装的包

apt-get source package
# 下载该包的源代码

apt-cache show package
# 获取包的相关信息，如说明、大小、版本等

sudo apt-get -f install
# 修复安装

apt-cache search package
# 搜索软件包

apt list --upgradeable
# 列出可更新的软件包及版本信息

sudo apt install package
# 安装指定的软件

sudo apt-get dist-upgrade
# 升级系统

apt-cache pkgnames
# 列出所有软件包的名字

apt-cache showpkg package
# 显示某个软件包的全面信息

apt-cache depends package
# 了解使用该包依赖那些包

apt-cache rdepends package
# 查看该包被哪些包依赖

sudo apt-get remove package
# 删除包

sudo apt-get install package
# 安装包

sudo apt-get build-dep package
# 安装相关的编译环境

sudo apt install libglib2.0-dev
# 安装glib

sudo apt-get remove package --purge
# 删除包，包括配置文件等

sudo apt-get install package --reinstall
# 重新安装包

sudo apt-get clean && sudo apt-get autoclean
# 清理无用的包
```

### 6、grep

```shell
 apt search fcitx | grep 安装
```

### 7、df

磁盘空间占用情况

```shell
df /
```

### 8、man

显示系统手册页（manual pages）的命令行工具。系统手册页包含了关于系统命令、库函数、系统调用等的详细文档和用法说明。

```c
man [section] command
```

`man` 是一个在 Unix 和类 Unix 系统上用来显示系统手册页（manual pages）的命令行工具。系统手册页包含了关于系统命令、库函数、系统调用等的详细文档和用法说明。

**基本用法**

```
bashCopy code
man [section] command
```

- `command`: 要查看手册页的命令或主题。
- ```
  section
  ```

  : 可选参数，指定手册页的章节。常见的章节包括：

  - 1: 用户命令
  - 2: 系统调用（内核函数）
  - 3: 库函数
  - 5: 配置文件格式
  - 7: 杂项（如宏、协议、设备）
  - 8: 系统管理命令

**选项**

- `-k keyword`: 在手册页中搜索关键字。
- `-f command`: 显示命令的简短描述。
- `-a`: 显示所有与关键字匹配的手册页。
- `-w`: 显示手册页文件的路径。

## 四、目录与文件相关命令

### 1.目录结构说明

#### 1.1 目录树

在学习文件相关命令之前，我们先来看一Linux的文件系统；

1. Linux有个基本思想：一切都是文件。就是系统中的所有都可以归结为一个文件，包括命令、硬件和软件设备、操作系统、进程等等对于操作系统内核而言，都被视为拥有各自特性或类型的文件。而这些文件是以一定的结构组织的；
2. linux系统的**目录结构是一个以/（根目录）为根节点的树形结构：**

![1635994868898](images\1635994868898.png)

#### 1.2.linux中的目录

- bin 存放二进制可执行文件（命令），所有用户都可以使用
- sbin 存放二进制可执行文件，只有root用户可以使用
- boot 系统启动需要的核心文件
- dev 设备文件
- **home 用户的主目录，每一个非root用户都会在home有一个为用户名的文件夹**
- lib 系统共享动态链接库
- mnt 挂载目录，通常挂载到该目录下，一般操作硬件会用到
- **etc 存放系统配置文件目录**

    /etc/passswd 用户信息文件

    /etc/group 用户组信息文件

- var 用来存放常态性变动的文件，经常发生变化的文件。比如缓存、登录文件，mysql数据库文件等
- tmp  临时目录，用来存放临时文件
- proc 虚拟目录，是内存的映射，可以访问到内存中的系统信息
- sys 和proc类似，存放和内核相关的信息和数据
- **root root用户的主目录**
- usr 存放应用程序和文件（类似于windows的system32、program files目录）

    **/usr/local/src  所有应用程序包都放这儿**

    **/usr/local/ 所有的应用程序都安装在这个目录。**

    /usr/bin、/usr/sbin：这是对/bin、/sbin的一个补充

注：

- /usr：系统级的目录，可以理解为C:/Windows/，/usr/lib理解为C:/Windows/System32
- /usr/local：用户级的程序目录，可以理解为C:/Program Files/，用户自己编译的软件默认会安装到这个目录下；
- /opt：用户级的程序目录，可以理解为D:/Program Files/，opt有可选的意思，这里可以用于放置第三方大型软件（或游戏），当你不需要时，直接rm -rf掉即可

1. 一般跟系统有关的程序或软件或需要进行配置的程序安装到/usr/local中（jdk，tomcat等），安装包可以放在/usr/local/src中；
2. 一般第三方与系统不是很挂钩的软件放在/opt中，安装包也可以放在这里；

### 2.文件夹命令（后面用到再说）

相对路径和绝对路径

- `/` 代表根目录，凡是最左边是一个 `/`代表绝对路径，即从根目录开始
- `./` 代表当前目录
- `../` 表示上一级目录

##### 2.1.mkdir -p : 创建目录

递归创建目录 `mkdir -p /a/b/c`

##### 2.2.rm -rf ：删除文件/目录 force

强制删除文件或者目录 ,该命令慎用，切记乱删除东西。

##### 2.3.mv ：移动

   mv x y:如果y不存在，就是改名，如果存在就移动x到y下面

##### 2.4.cp -rf: 复制

- 将dir1复制一份到dir2目录（包含目录内容一起复制） : cp -rf dir1 dir2
- 直接复制内容（非常重要） : cp -rf dir1/* dir2

##### 2.5.tar :打包压缩

- 打包 : tar -czvf tom.tar.gz  tom   把 tom 打包成tom.tar.gz  c-->compress
- 解包： tar -xzvf tom.tar.gz -C /usr/local  - 把tom.tar.gz解压到/usr/local   x-->uncompress
- 参数：

```
	-c	创建一个新的tar文件         
   -t	参看压缩文件内容
	-v	显示运行过程信息
   -j	调用bzip2压缩命令执行压缩
	-f	指定文件名称	    
   -C 指定需要解压到的目录
	-z	调用gzip压缩命令执行压缩   
	-x	解开tar文件
```

##### 2.6.zip 打包压缩

   unzip Soft.zip -d ./soft/  解压缩

   zip -r -o -q soft1.zip soft/ 压缩

    -r 递归，就是将这个文件夹下面所有的内容都压缩

   -o 输出信息，后面跟压缩的名字

   -q 安静的压缩，就是不需要输出压缩的信息

### 3.文件相关命令（后面用到再说）

##### 3.1.touch : 创建文件

- touch 1.txt

##### 3.2.cat : 查看文件内容

- cat 1.txt

##### 3.3.tail -f : 查看文件尾部

- tail -f  -n 5 notes.log  ： 查看末尾5行 ，通过 -n指定行数
- echo  heelo33 >> hello.txt;

## 五、进程的相关命令

### 1、ps

**功能**

    显示当前进程的状态 (Process Status)

**语法**

    ps [options]

**常**⽤语法选项

- -A ： 列出所有的进程
- -e : 与 -A 功能类似
- -w ： 显示加宽可以显示较多的资讯
- -au : 显示较详细的信息
- -aux ： 显示所有包含其他使⽤者的进程

### 2、top

**功能**

    实时显示进程的信息

**选项**

- d : 改变显示的更新速度，或是在交谈式指令列 (interactive command) 按 s
- q : 没有任何延迟的显示速度，如果使⽤者是有 superuser 的权限，则 top 将会以最⾼的优先 序执⾏
- c : 切换显示模式，共有两种模式，⼀是只显示执⾏档的名称，另⼀种是显示完整的路径与名称
- S : 累积模式，会将⼰完成或消失的⼦进程 (dead child process) 的 CPU time 累积起来
- s : 安全模式，将交谈式指令取消, 避免潜在的危机 i : 不显示任何闲置 (idle) 或⽆⽤ (zombie) 的进程
- n : 更新的次数，完成后将会退出 top
- b : 批次档模式，搭配 “n” 参数⼀起使⽤，可以⽤来将 top 的结果输出到档案内

### 3、kill

**功能**

    ⽤于结束进程的命令或者⽤于显示相关信号

**语法**

    kill [选项] [参数]

**选项**

- `-signal`: 指定要发送的信号类型，其中 `signal` 可以是信号名称（如 `SIGTERM`）或信号编号（如 `15`）。
- `-l`: 列出系统支持的所有信号。

**常用信号**

- `SIGTERM` (15): 默认信号，用于请求进程正常终止。
- `SIGKILL` (9): 强制终止进程，不允许进程清理资源。
- `SIGINT` (2): 中断信号，通常由键盘输入 `Ctrl+C` 触发，用于中断正在运行的进程。
- `SIGHUP` (1): 挂起信号，通常用于通知进程重新加载配置文件或重新启动。
- `SIGSTOP` (19): 停止进程运行，暂停进程的执行。

### 4、pidstat

**功能**

用于监视进程资源使用情况的命令行工具。它可以显示各个进程的 CPU 使用率、内存占用、I/O 等情况。

**用法**

    pidstat [options] [interval [count]]

- `interval`: 指定输出信息的时间间隔，单位为秒。
- `count`: 指定输出信息的次数。

**选项**

- `-u`: 显示 CPU 使用情况。
- `-r`: 显示内存使用情况。
- `-d`: 显示 I/O 使用情况。
- `-t`: 显示线程级别的信息。
- `-p pid [, pid]...`: 指定要监视的进程 ID。

### 5、pstree

**功能**

    用于以树形结构显示进程之间的关系。它可以显示当前系统中运行的所有进程，并以树形结构显示它们之间的父子关系。

**用法**

    pstree [options] [pid]

**选项**

- `-p`: 显示进程的 PID。
- `-u`: 显示进程的所有者。
- `-a`: 显示进程的命令行参数。
- `-l`: 使用长格式显示。
- `-n`: 不对输出进行排序。
- `-h`: 显示帮助信息。

## 六、开机自启动

    要在 Ubuntu 下开机时执行`~/.test` 脚本，你可以将其添加到用户的启动文件中。你可以选择使用 `~/.bash_profile`、`~/.bashrc`、`~/.profile` 或者 `~/.bash_login` 中的任何一个文件。这些文件会在用户登录时执行。
    
    通常，用户在`~/.bash_profile` 或 `~/.profile` 文件中添加启动脚本，因为这些文件只在用户登录时执行一次，避免了重复执行。而 `~/.bashrc` 文件在每次新开一个终端时都会执行。

你可以编辑其中任何一个文件，添加如下内容：

```shell
# 执行 ~/.test 脚本
~/.test

```

如果 `~/.test` 是一个可执行的脚本文件，你需要确保它具有执行权限。你可以使用 `chmod` 命令添加执行权限：

```c
chmod +x ~/.test

```
