# git的安装

## git的官网

[https://git-scm.com/](https://git-scm.com/)

## Windows安装git

![](/assets/深度截图_选择区域_20181018142452.png)

这里点击一下，然后安装即可

## linux安装git

在linux下面的安装，推荐使用yum 或者apt-get安装

```
 yum install git  
 apt-get install git
```

此外，也可以通过编译安装的方式进行安装。  
 不过，要先安装一些依赖

```
yum install curl-devel expat-devel gettext-devel  openssl-devel zlib-devel
cd /home/git-XXX #进入到git的解压目录
./configure
make prefix=/usr/local all #需时久
make prefix=/usr/local install
```

## 安装成功

输入

```
git --version
```

出来了说明git安装成功

# IDE的安装与使用

原始的git命令行简单却又生涩。  
一般用命令行的不是大神就是菜鸡。  
大神嘛...我估计看到我的第一章就直接跳过进第二章了...  
所以建议各位还是使用ide避免出错  
这里我推荐使用两款ide，一款是sourceTree，一款是SmartGit

## SmartGit

给你瞅一眼大概长什么样子

![](/assets/深度截图_选择区域_20181019091152.png)

上面彩色的图标都这么生动形象了。我就不说啥了。

主要是合并（Merge）的操作，SmartGit会自带一个conflictResolver可以解决合并过程中的冲突，相当好用。

## SourceTree

由于我的系统是linux没有截图，但是SourceTree的使用方法和SmartGit大同小异。区别主要是  
1.界面更好看了  
2.有中文字体  
3.注册较为麻烦、内存消耗大

