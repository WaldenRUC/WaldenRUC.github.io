---
title: 'Aliyun Server'
date: 2022-12-15
permalink: /posts/2022/12/Aliyun-Server/
tags:
  - Linux
---
## 从零开始配置阿里云服务器

### 1. 购买服务器 & 基础配置

首先在阿里云注册，买一个最低配的服务器，花费96元。我买的是CentOS 8.2版本，记下公网IP，更改密码。

然后为了防止ssh上之后挂机自动掉线，修改【/etc/ssh/sshd_config】文件，找到ClientAliveInterval两行（可以用/Client指令定位）

<img src="/images/aliyun_server/image-20210312201127731.png" alt="image-20210312201127731" style="zoom:50%;" />

保存退出（:wq），并重启服务即可：

```shell
systemctl restart sshd.service
```

注意到我们的主机名（也就是命令行@后的字符串）挺难看，因此我们可以在/etc/hostname文件中修改：

```shell
vi /etc/hostname
```

按【dd】删除，然后写上自己喜欢的称呼即可（此部分为配置好环境后才补充的，因此下文截图中可能尚未更改主机名）。最后输入：

```shell
reboot
```

安全重启服务器，才能将刚才的改动更新到内核中。重启和注销（exit）不一样，重启后需要等待一段时间才能重新连上服务器。

BTW，我们使用termius软件管理服务器，并可以利用SFTP协议可视化地传文件，非常方便！

### 2-1. 开发工具（python）配置

（此部分参考[配置教程](https://www.cnblogs.com/simuhunluo/p/7704765.html)）

首先安装python，切换到~目录，并在命令行执行：

```shell
wget https://www.python.org/ftp/python/3.8.3/Python-3.8.3.tgz
tar -xvf Python-3.8.3.tgz
```

（虽然现在的python版本已经迭代到3.9了，但我还是比较喜欢3.8的版本）

然后安装必要的依赖：

```shell
yum install -y gcc zlib zlib-devel openssl-devel readline readline-devel libffi-devel bzip2-devel mesa-libGL.x86_64 xz-devel
```

（注意这个libffi-devel是必须要安装的！否则将来安装跨编程语言的第三方包时，会出现混合编程报错_ctypes模块缺失的错误。如果出现了错误，则yum安装libffi-devel，**<u>然后重新编译python</u>**【参考下文的步骤，依旧不能遗漏--prefix选项参数！】）

（mesa-libGL.x86_64是cv2的一个附属包，不安装会报错，安装后不用重新编译python）

（xz-devel是lzma的C语言依赖库）

下载后需要编译，执行以下命令：

```shell
cd Python-3.8.3/
./configure --prefix=/usr/local/python3Dir
make
make install
```

为刚刚下载的python3.8创建软链接，这样可以把两个bin目录关联起来，今后就可以调用/usr/bin下的python3命令了。

```shell
cd /usr/bin
ln -s /usr/local/python3Dir/bin/python3 /usr/bin/python3
```

在当前目录（/usr/bin）检查一下：

```shell
ll -a python*
```

![image-20210315185900343](/images/aliyun_server/image-20210315185900343.png)

最终成果：

![image-20210315190014646](/images/aliyun_server/image-20210315190014646.png)

Q：为什么不将python3.8连接到python指令？

A：改掉python的软链接会出现很多问题，有些python2的文件是用python引用的，因此若改掉【python】指向的二进制文件，则这些脚本可能会出问题。例如yum软件是引用python指令作为PYTHONPATH，且基于python2，若改为python3.x，则无法编译通过。

当然，在编写程序使用vim的时候，我个人是喜欢开启行号的。如果需要自定义配置，则**新建**一个~/.vimrc文件：

```shell
vi ~/.vimrc
```

写入：

```shell
set tabstop=4
set softtabstop=4
set shiftwidth=4
set autoindent
set cindent
set cinoptions={0,1s,t0,n-2,p2s,(03s,=.5s,>1s,=1s,:1s
set nu
set ruler
set hlsearch
set backspace=2
syntax enable
set background=dark
:colorscheme molokai
```

保存退出。这样每次使用vim命令时都会使用.vimrc中的配置。

molokai.vim主题文件可以在github上下载，然后放在【~/.vim/colors】文件夹下。

***

### 2-2. git配置

先在【https://github.com/git/git/archive/v2.17.0.tar.gz】上下载git(v2.17)，然后用SFTP传到服务器上，然后解压并进入目录：

```shell
tar -xvf git-2.17.0.tar.gz
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker
yum remove git 
cd git-2.17.0/
make prefix=/usr/local/git all
make prefix=/usr/local/git install 
```

然后在/etc/profile中，最后一行加入：

```shell
export PATH=$PATH:/usr/local/git/bin
```

保存并退出，然后执行：

```shell
source /etc/profile
```

### 2-3. Shell配置

首先安装zsh：

```shell
sudo yum install -y zsh
```

然后检查一下是否安装成功：

```shell
cat /etc/shells
```

<img src="/Users/walden/通用笔记备忘/image-20210312232152161.png" alt="image-20210312232152161" style="zoom:50%;" />

下载oh-my-zsh（因为我喜欢它的界面，哈哈，不喜欢的读者朋友们可以跳过此步~）：

```shell
curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh
```

完成界面如下：

![image-20210316115226115](/images/aliyun_server/image-20210316115226115.png)

更改默认shell：

```shell
chsh -s /bin/zsh
```

更改配置文件.zshrc：

```shell
vi .zshrc
```

![image-20210316115540820](/images/aliyun_server/image-20210316115540820.png)

找到ZSH_THEME变量，将其改为random（参考14行的注释说明）

exit退出后重新登录服务器，可以看到通过上文更改默认shell为zsh，同时配置了随机的主题风格后，每次都有全新的主题终端。

![image-20210316120017074](/images/aliyun_server/image-20210316120017074.png)

### 2-4. 包管理

CentOS下的包管理用yum就行了，当然我个人认为yum软件与MacOS下的Homebrew相比还差点儿（也许是因为研究得不够深入？），不过先把必要的包安装好吧。

```shell
yum -y install pcre-devel openssl-devel lrzsz openssh-clients gcc libffi-devel
```

### 3-1. pip配置

默认的pip是python2的，因此我们需要先卸载python2的pip，然后再下载python3的pip。

```shell
python -m pip uninstall pip	#用python（python2）卸载pip
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py 
sudo python3 get-pip.py	#用python3下载pip并运行，这样pip就关联到这个python版本了
```

![image-20210316123912168](/images/aliyun_server/image-20210316123912168.png)

注意到警告，环境变量PATH中没有这个目录，因此**当前输入的pip命令无法找到对应的pip软件路径**。这样修改就可以了：

```shell
export PATH=/usr/local/python3Dir/bin:$PATH
```

修改后，安装必要的python库（因为是阿里云的服务器，所以不需要更改源了）

```shell
pip install selenium==2.48.0 numpy pandas opencv-python pytesseract Image backports.lzma
```

安装完pytesseract后，会出现问题【Python tesseract is not installed or it’s not in your path】，需要修改一下内部的文件。注意到安装路径为下图所示：

![image-20210316131942894](/images/aliyun_server/image-20210316131942894.png)

```shell
cd /usr/local/python3Dir/lib/python3.8/site-packages/pytesseract
vim pytesseract.py
```

![image-20210316132348121](/images/aliyun_server/image-20210316132348121.png)

修改第32行。

使用【which】命令，查找pytesseract命令是否已经安装到bin下了：

![image-20210316132657367](/images/aliyun_server/image-20210316132657367.png)

返回安装路径，然后将tesseract_cmd改为此路径即可。

![image-20210316132730973](/images/aliyun_server/image-20210316132730973.png)

关于PYTHONPATH变量缺失的问题：在/etc/profile中，补上最后一行：![image-20210316194829603](/images/aliyun_server/image-20210316194829603.png)

这个pythonpath的路径是**pip的下载第三方包路径**，pip下载第三方包后，存储在【pip --version】的返回路径下，对应图中的路径是【/usr/local/python3Dir/lib/python3.8/site-packages】，更改后，我们可以看到，sys.path中有了刚才添加的路径。

![image-20210316135530941](/images/aliyun_server/image-20210316135530941.png)

![image-20210316192014349](/images/aliyun_server/image-20210316192014349.png)

### 3-2. _bz2配置

关于_bz2缺失的问题，首先在网上下载【\_bz2.cpython-38-x86_64-linux-gnu.so】文件，然后sftp传到服务器上，执行以下操作：

```shell
mv _bz2.cpython-38-x86_64-linux-gnu.so /usr/local/lib/python3.8/lib-dynload
cd /usr/local/lib/python3.8/lib-dynload
chmod +x _bz2.cpython-38-x86_64-linux-gnu.so
```

### 3-3. pandas配置

此前pip下载【backports.lzma】是为了解决pandas的import报警，解决方案如下：

首先根据pip绑定的路径，打开python3.8的目录：

```shell
cd /usr/local/python3Dir/lib/python3.8
vi lzma.py
```

将下图被注释的代码片段改为：

![image-20210316213219448](/images/aliyun_server/image-20210316213219448.png)

### 3-4. phantomjs配置

首先从网络上下载【phantomjs-2.1.1-linux-x86_64.tar.bz2】，然后传到云服务器上。

```shell
tar -xvf phantomjs-2.1.1-linux-x86_64.tar.bz2
cd phantomjs-2.1.1-linux-x86_64 
yum install fontconfig freetype2
```

然后需要修改/etc/profile，在里面添加上phantomjs的bin文件路径，否则无法通过输入【phantomjs】命令启动软件（与前文同理）

```shell
vi /etc/profile
```

在79行添上相应内容（我的phantomjs是放在~（个人用户根目录）下的，所以这样设置）

![image-20210316231327337](/images/aliyun_server/image-20210316231327337.png)

安装leptonica库：

```shell
wget http://www.leptonica.org/source/leptonica-1.80.0.tar.gz
tar -xvf leptonica-1.80.0.tar.gz
cd leptonica-1.80.0
./configure
make && make install
```

然后在/etc/profile最后补上这三行，并【source /etc/profile】使之生效。

![image-20210317154858274](/images/aliyun_server/image-20210317154858274.png)

安装tesseract-ocr：

```shell
wget https://codeload.github.com/tesseract-ocr/tesseract/tar.gz/4.1.0#这一步骤也可以替换成通过在本机上下载然后sftp到云上
tar -xvf 4.1.0
cd tesseract-4.1.0/
./autogen.sh
yum install gcc gcc-c++ gcc-g77#安装依赖库
./configure
make && make install
sudo ldconfig
```

这样就安装了tesseract的bin文件。在pip下载的pytesseract安装包中，打开py文件，修改tesseract_cmd：

<img src="/Users/walden/通用笔记备忘/image-20210317162243282.png" alt="image-20210317162243282" style="zoom:50%;" />

解决上述问题后，出现错误如下图所示：

![image-20210317162204858](/images/aliyun_server/image-20210317162204858.png)

说明没安装语言包。在csdn上下载必要的语言包：

<img src="/Users/walden/通用笔记备忘/image-20210317162918935.png" alt="image-20210317162918935" style="zoom:33%;" />

将这三个sftp到云上新建的~/tessdata文件夹下，然后输入：

```shell
mv tessdata/* /usr/local/share/tessdata/
```

完成。

### 4. crontab配置



