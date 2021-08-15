# 关机命令

关机指令为shutdown



sync：将数据同步到硬盘中

shutdown：关机

shutdown -h 10：10分钟后关机

shutdown -h now：立刻关机

shutdown -h 20:25：今天20:25关机

shutdown -h +10：十分钟后关机

shutdown -r now：重启

shutdown  -r +10：10分钟后重启

reboot：重启

halt：关闭系统

# 系统目录结构

1. 一切皆文件
2. 根目录/，所有的文件都挂载在这个节点下

# 常用的基本命令

## 目录管理

cd：切换目录

./：当前目录

cd..：返回上一级



### **ls：列出目录**

参数

​	-a：查看全部，包括隐藏文件

​	-l：列出所有的文件，包含属性和权限，没有隐藏文件



### **cd命令 切换目录**

cd 目录名(相对路径，绝对路径，绝对路径以/开头，相对目录使用../表示上一级)

pwd：查看当前登录用户

cd ~：切换到用户目录

mkdir：创建一个目录

mkdir -p ：递归创建目录



### **rmdir命令 删除目录**

rmdir -p 递归删除多个目录

rmdir 仅能删除空的目录

### cp命令 复制

cp 文件名 目标路径

文件重复会让选择覆盖或放弃



### rm命令 移除文件或者目录

参数：

-f 忽略不存在的文件，不会出现警告，强制删除

-r 递归删除目录

-i 互动，删除询问是否删除

re -rf /   系统中的所有文件都被删除，删库跑路

### mv 移动文件或目录

**格式**

mv 文件名 目标路径

**重命名文件夹**

mv 文件夹名 新文件夹名



参数：

-f 强制

-u 只替换已经更新过的文件

## 修改文件属性

### chgrp

chgrp  -R  属组名  文件名

-R为递归修改



### chown

更改文件属主，也可以同时更改文件属组

```
chown  -R  属主名  文件名
chown  -R  属主名:属组名  文件名
```

### chmod

更改文件的9个属性

Linux文件属性有两种设置方法，一种数数字，一种是符号

```
r:4   w:2   x:1
```

chmod  770  文件名

属主和属组拥有全部权限，其他组无权限

## 文件内容查看

### cat

由第一行看是显示文件内容



### tac

从最后一行开始显示



### nl

显示的时候，顺道输出行号



### more

一页一页的显示文件内容

空格翻页，回车往下一行，:f行号



### less

退出q命令

与more类似，可以往前翻页

空格下翻一页，pageup和pagedown键控制

查找字符串：/要查询的字符串(向下查询，要向上查用?)

n继续找下一个，N找上一个



### head

只看头几行



### tail

tail -n  行数

只看后几行



### ip addr

查看网络配置

### ln

创建连接，默认为硬连接，加-s是软连接、

### touch

创建文件

### echo

echo "  " >> 文件

输入字符串到文件中

# vim

vi/vim分为三种模式，分别是命令模式，输入模式，底线命令模式

## 命令模式

刚启动vi/vim就进入了命令模式

敲击键盘会被识别为命令

常用命令：

1. i，切换到输入模式，以输入字符
2. x，删除当前光标所处的字符
3. :，切换到底线命令模式，在最低一行输入命令
4. esc，退出输入模式

## 输入模式

输入模式可以使用以下按键

1. 字符按键以及shift组合，输入字符
2. enter，回车键，换行
3. 退格
4. del，删除键，删除光标后一个字符
5. 方向键，在文本中移动光标
6. home/end，移动光标到行首/行尾
7. page up/page down翻页
8. insert，切换光标为输入/替换模式
9. esc，退出输入模式

## 底线命令模式

命令模式下按下：就进入了底线命令模式，光标移动到了最底下

可以使用wq，保存并退出

# 用户管理

## useradd 命令，添加用户

useradd -选项 用户名

-m：自动创建这个用户的主目录

## userdel 删除用户

userdel -r 用户名

删除用户时将目录一起删掉

## usermod 修改用户

usermod 选项 用户名

## 切换用户

su 用户名

**退出普通用户**

exit

## 设置用户密码

passwd 用户名

使用root用户操作

## 锁定用户

passwd -l 用户名

锁定后用户不能登录

passwd -d 用户名

删除该用户密码，并且不能登录

# 用户组管理

## groupadd 创建用户组

创建完后可以得到一个组的id，这个id可以指定，（使用-g 数字）如果不指定就是自增1

## groupdel 删除用户组



## groupmod 修改用户组

后接参数表示修改内容

-g 修改id

-n 修改名字

## 切换用户

登录用户后使用newgroup命令

一般在创建用户的时候使用-G指定用户组

# 磁盘管理

## df 列出文件系统整体的磁盘使用量



## du 检查磁盘空间使用量

# 进程管理

## 基本概念

1. 在Linux中，每一个程序都有自己的一个进程，每一个进程都有一个id号
2. 每一个进程都有一个父进程
3. 进程可以有两种存在方式，前台和后台
4. 服务一般在后台运行，基本的程序都是在前台运行

## 命令

**ps 查看当前系统中正在执行的各种进程的信息**

参数

1. -a 显示当前终端运行的所有进程信息
2. -u 以用户的信息显示进程
3. -x 显示后台运行进程的参数

```bash
# ps -aux 查看所有的进程
ps -aux|grep mysql
ps -aux|grep redis

# | 管道符
# grep 查找文件中符合条件的字符串
```

ps -ef 可以看到父进程信息

pstree 可以显示结构树

参数：

-p 显示父id

-u 显示用户组

# 环境安装

安装软件一般有三种方式，rpm，解压缩，yum在线安装

## jdk安装

1. 下载jdk rpm

2. 安装java环境

   ```
   卸载：
   	rpm -qa|grep jdk 检测jdk版本信息
   	rpm -e --nodeps jdk8 卸载
   	
   安装：
   	rpm -ivh 
   ```

   ```
   环境变量
   配置到/etc/profile
   JAVA_HOME=/home/zero/Documents/jdk-8u301-linux-i586/jdk1.8.0_301
   	CLASSPATH=.:$JAVA_HOME/lib/tools/jar
   	PATH=$JAVA_HOME/bin:$PATH
   	export JAVA_HOME CLASSPATH PATH
   	
   	
   最后执行source命令让配置文件生效
   source /etc/profile
   ```
   
   

## 发布项目

将jar包传入服务器

开启防火墙对应的端口

```
forewall-cmd --zone=public --add-port=8080/tcp --permanent
```

重启防火墙

```
systemctl restart firewalld.service
```

查看开启的端口

```
firewall-cmd --list-ports
```

执行项目

```
java -jar jar包名
后台运行需要使用nohup
```

