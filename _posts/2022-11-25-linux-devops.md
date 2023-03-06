---
title: 'Linux DevOps'
date: 2022-11-25
permalink: /posts/2022/11/linux-devops/
tags:
  - Linux
  - Python
---

## Linux运维技巧

- [Vim思维导图](/files/Vim基本操作思维导图.pdf)

## Q: Python pip下载第三方库速度太慢

A: 

```
pip install <pkg-name> -i https://pypi.tuna.tsinghua.edu.cn/simple
```

***

## Q: 终端连接服务器总是断开？

A: 

```
sudo vi /etc/ssh/sshd_config
```
将【ClientAliveInterval 0】改为 【ClientAliveInterval 30】

将【ClientAliveCountMax 3】 改为 【ClientAliveCountMax 86400】

```
sudo service sshd restart
```

***

## Q: Ubuntu无法使用jps命令

A:

ubuntu下没有jps命令：安装openjdk-17-jdk-headless解决（具体版本依据服务器的java版本确定

***

## Q: 无法访问hadoop启动的网页

A:

因为默认开启了**防火墙**。

```
CentOS:
sudo systemctl disable firewalld.service
service network restart

Ubuntu:
sudo ufw disable
reboot
```
操作结束后即可通过网页访问

***

## Q: ssh无法连接远程服务器(192.168.199.1)

A: 无法连接服务器可能是因为**ssh的端口被block**，因此需要从**物理管理端口iBMC**进入服务器：用KVM软件登录【192.168.30.115】，用/密【root/g______h】
切换到root用户，关闭ufw防火墙：【ufw disable】，并【reboot】重启即可

***

## Q: 更改CentOS的IP地址

A: CentOS的IP地址可以通过【ip addr】来设置。

网卡配置文件在【/etc/sysconfig/network-scripts】中，如果网卡是eth0，则编辑【vi ifcig-eth0】，进入文件。

假设要修改的IP为【192.168.199.1】，则修改：
```
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.199.1
GATEWAY=192.168.199.254
NETMASK=255.255.255.0
DNS1=8.8.8.8
DNS2=119.29.29.29
```

最后输入
```
sudo service network restart
```
重启服务

***

## Q: 如何在服务器A上免密登录服务器B

A: 在A上运行ssh-keygen，在~/.ssh/下找到id_rsa.pub，将其上传到
服务器B的~/.ssh/authorized_keys文件末尾

***

## Q: bash脚本单引号和双引号的区别

A: 单引号显示的是【纯文本】，而双引号会显示引号内的【变量】对应的值

例如：
```
echo '$LANG' 与 echo "$LANG"
```
前者显示的是"\$LANG"，后者显示的是"zh-CN"

***

## 出现cleanMyMac删除不掉的软件怎么办

在终端输入【top | grep CCX】，其中CCX代表着软件删不掉的软件；

然后看到进程号，如822，在终端输入【kill -9 -822】即可。

然后用cleanMyMac删除该软件即可。

## 配置Crontab的常见问题

1. 直接执行bash文件，需要加上755权限，以及在文件第一行加上【#!/usr/bin/bash】
2. **sendmail: fatal: parameter inet_interfaces: no local interface found for ::1**[问题解决链接](https://blog.csdn.net/weixin_44058932/article/details/105185155?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161778787416780266218081%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=161778787416780266218081&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-105185155.first_rank_v2_pc_rank_v29&utm_term=sendmail%3A+fatal%3A+parameter+inet_interfaces%3A+no+local+interface+found+for+%3A%3A1)
3. 脚本、crontab中命令尽量写绝对路径
4. 脚本内要添加source bashfile

***

## git clone 下载慢的方法

将clone后的github.com改为github.com.cnpmjs.org。

例如：

```shell
git clone https://github.com.cnpmjs.org/lidingruc/2019R.git
```

速度能变快。

其余修改DNS等方法效果不好，不知道为什么。更好的解决方案就是用梯子翻墙。

## Homebrew在update的时候遇到问题的解决方案

如果执行brew update时提示报错：homebrew-cask is a shallow clone

则命令行运行

```rm -rf /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask
rm -rf /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask
brew update
```

即可。

## bash下所有命令失效

原因：错误更改了环境变量，导致命令无法在路径中找到。

解决方法：在终端更改PATH变量：

```shell
PATH=/bin:/usr/bin:/usr/local/bin:$PATH
```

然后当前环境下就可以使用/bin、/usr/bin、/usr/local/bin下的命令了，如果有必要，可以把/sbin、/usr/sbin、/usr/local/sbin加上去，这样方便重启（reboot）

回头再在/etc/profile配置文件中修改PATH即可（这个文件前半部分是添加bin、sbin等必要命令；后半部分是添加用户自己下载软件的二进制文件路径）

## VMWare Tools安装方法

以windows虚拟机为例，先在工具栏找到CD/DVD(SATA)，然后选择“选择光盘或光盘映像”，在跳出的isoimages文件夹中找到windows.iso文件（该文件夹下还有诸如linux.iso文件等等）

## 提示dmg文件已经损坏

在终端输入以下代码以实现允许所有来源的软件运行：

```
sudo spctl --master-disable
```

输入以下代码以实现只允许来自苹果的软件运行：

```
sudo spctl --master-enable
```

对于单个软件，需要**将其拖动到桌面**，然后在终端输入以下代码：

```
sudo xattr -r -d com.apple.quarantine <$appname.app>
```

***


XCode: command + R 编译并运行程序


终端字体变大：command+’+’

终端字体变小：command+’-’

ls: 显示当前路径下的所有文件和文件夹

cd: 将当前路径跳转到……文件夹/文件		

cd .. 	跳转到上一级路径

pwd: 显示当前路径

ls -l: 显示文件夹的详细信息

man ls: 显示ls命令的帮助文档

history: 查看历史终端命令（即自己曾经输入过的命令）

ls -a: 显示目录下的所有子目录和文件，包括隐藏文件。

ls -h: 配合-l以人性化的方式显示文件大小

touch …:创建一个文件在当前目录

ls *.py: 显示以后缀为.py的所有当前目录下的文件

ls 和 ls * 等价

*：表示一个或多个字符

?：表示一个字符

例子：ls *.p?

[]: 表示匹配中括号内的任意一个字符

例子：1[23]3 可以匹配123\133

例子：1[1-9]3 可以匹配113到193的九个字符串

ls a-f 寻找的是a-f为名字的文件！在中括号外，-将失去通配的作用

*.a 匹配的是所有以.a为后缀的文件，但是

\*.a匹配的只有*.a，\是转义符

开启编辑器：gedit example.txt

cat example.txt :显示文件内容

more example.txt: 一页一页显示文件内容 

A | B: 一个命令的输出可以通过管道作为另一个语句的输入。

即：从A读，写入B

例子：ls -lha | more

cd ~ : 跳转到根目录(/walden)

cd _: 跳转到上次所在的目录

A > B: 将B中的内容替换为A

A >> B : 将A的内容补充到B的末尾

例子：ls -lha > ls.txt:    ls.txt内容为当前目录下的文件列表名称

例子：ls -lha >> ls.txt:  在ls.txt后补充当前目录下的文件列表名称

touch 创建一个文件（不一定要在当前目录，可以使用相对、绝对路径）

例子：touch ./myself/123.py

mkdir 创建一个目录（文件夹）

rmdir 删除一个目录（文件夹）：注意：只允许删除空的文件夹！

rm 删除一个文件（无法删除目录！）

rm my_document -r : 删除文件夹及文件夹下的所有文件

rm -f: 强制删除，忽略不存在的文件，无需提示

rm -rf: 上两句的结合

创建快捷方式：
命令：ln 源文件 链接文件			（硬链接）
或： ln -s 源文件 链接文件	（软链接）

软链接：源文件删除后，链接文件打不开。

硬链接：源文件删除后，链接文件能打开（但修改源文件时，链接文件也会随之改变）。硬链接相当于给一个文件添加另一个名字。当rm命令删除该文件时，改成删除其一个名字。当最后一个名字被删除后，这个文件就被真正删除。

文件的合并与重定向：
cat A.py B.py > C.py
则C.py的内容为A的内容（靠前）+B的内容（靠后）

文件内搜索：用grep
grep -option ‘string’ A.py 	在A.py中搜索string字符串（字符串可以正则）
option:
	-n: 搜索后显示目标字符串所在行数

find 操作：搜索特定目录下符合条件的文件

find 路径 -name A.py

cp: 将文件或目录 复制到另一个文件夹中
例子：cp A.py test/
如果把文件夹复制到另一个文件夹中，需要在命令后加 -r
例子：cp test/ test2/ -r
cp -v: 显示复制进度

mv: 剪切或重命名
mv A.py test/ 即把A.py剪切到test文件夹中
如果要把文件夹复制到文件夹内，需要加-r
如果mv后跟的两个命令没有文件夹，则
mv A.py B.py 即把A.py的名称改为B.py

tar [] 打包文件名 文件
将指定文件打包成一份文件夹（没有压缩）
gzip 可以将tar后缀的文件压缩，变为.tar.gz的格式

gzip -d: 解压

tar -zxvf: 既打包，又压缩

tar -xvf: 解压tar文件夹

压缩文件：zip
解压文件：unzip

查看命令位置：which

终端下，如果是$符号，就是普通用户。如果需要转成管理员，则输入：
sudo -s
再输入密码，就是管理员了。此时不是$，而是#符号。

date: 显示当前时间

ps: 查看进程信息

exit: 从管理员退出，回到普通用户身份

control(^) + C : 强制结束当前终端的进程（进程，即正在运行的程序）

kill 12345: 结束编号为12345的进程

kill -9 12345: 强制结束编号为12345的进程

reboot: 重新启动

shutdown: 关机（与reboot一样需要管理员（#）权限）。shutdown后可以加-h，指定关机时间

top: 显示当前资源的利用率的情况

df: 查看磁盘空间

du: 查看当前目录下的空间

ifconfig: 显示网络接口卡（可以查看自己的ip地址）

ping: 测试远程主机连通性。ping + ipv4地址

who: 查看登录的用户

whoami: 查看正在操作的用户

ssh: 远程登录电脑

passwd + [user]: 更改用户的密码

su - [user]: 切换到该用户（需要输入密码），此时目录也迁移过去了

useradd [name]  -m: 添加用户，到当前列表
  
cat /etc/group : 查看有哪些用户组（显示/etc/group下的文件内容）

groups [username: walden] : 显示指定用户账户的组群成员身份

groupmod: 查看群组

groupadd: 添加一个群组

usermod -a -G [sudo] [username]: 将user添加到sudo群组中

chmod: 更改文件权限。例子：chmod o+w 1.py: 把其他用户对1.py的权限增添“可写”
	u: 文件所有者；g: 同组用户；o: 其他用户
另一种方法：r:4,w:2,x:1,-:0

chmod 751 [filename]: 所有者用户：可读可写可执行；同组用户：可读可执行；其他用户：可执行

cat: 读；

vi: 写；

./[filename]: 执行

gcc + [filename]: 编译文件

修改文件所有者： chown [user] [filename]

修改文件的所属组：chgrp [groupname] [filename]

scp a.txt 192.168.21.188:/home/python/Desktop			把本地文件上传到服务器指定目录下

scp 192.168.21.188:/home/python/Desktop/b.txt ./ 		把服务器上路径的文件拷贝到当前目录下

在vim模式下，
:wq		保存且退出
:x		保存且退出

运行python程序的方法：python a.py 或 python3 a.py

使python支持中文：代码前加#encoding = utf-8


## 修改Mac应用图标

将图标命名为xxx.png，并将图片尺寸修改为1024x1024.假设该图标在Dir文件夹下。
在Dir文件夹下，创建临时目录Hexchat.iconset, 然后在终端输入以下指令：
```shell
sips -z 16 16     xxx.png --out Hexchat.iconset/icon_16x16.png
sips -z 32 32     xxx.png --out Hexchat.iconset/icon_16x16@2x.png
sips -z 32 32     xxx.png --out Hexchat.iconset/icon_32x32.png
sips -z 64 64     xxx.png --out Hexchat.iconset/icon_32x32@2x.png
sips -z 128 128   xxx.png --out Hexchat.iconset/icon_128x128.png
sips -z 256 256   xxx.png --out Hexchat.iconset/icon_128x128@2x.png
sips -z 256 256   xxx.png --out Hexchat.iconset/icon_256x256.png
sips -z 512 512   xxx.png --out Hexchat.iconset/icon_256x256@2x.png
sips -z 512 512   xxx.png --out Hexchat.iconset/icon_512x512.png
cp xxx.png Hexchat.iconset/icon_512x512@2x.png
```
生成icns文件：
```shell
iconutil -c icns Hexchat.iconset
```
如果要删除，则
```shell
rm -R Hexchat.iconset
```
接下来，找到要修改的应用，显示简介：
```shell
command + i
```
将刚才生成的Hexchat.icns拖入其中即可。

## python在终端打印带颜色的print

```python
print("\033[0;37;40m\tHello World\033[0m")
```
格式：

开头部分：\033[<显示方式>;<前景色>;<背景色>m + 结尾部分：\033[0m