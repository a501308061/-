# Linux 进程管理

## 环境挂了
一般在我们的服务器中，环境是很难挂的。因为H5与后台不大一样，后台要承担运算的职责，需要消耗大量的内存和cpu资源。所以环境挂了是情有可原的。
但是很难挂不代表不会挂。下面就来讲一下基本的操作。

## 查看进程
```
ps -ef |grep nginx
```
这里就以nginx为例，查看是否服务被停掉了。 
上面的这条命令可以分作两条看待。  
第一条是 ps -ef 列出所有进程。但是这个进程也太多了趴。很多其他系统基础的进程我们是不需要关系的。所以有了第二条筛选语句grep。  
而其中的连接符号|，称作管道。  
管道是什么？  
就是把一头的东西送往另一头。  
在Linux中，就是把管道前输出（ps -ef）的内容当做管道后(grep )的输入。  
如果正常的话大概长这个样子  
![](/assets/深度截图_选择区域_20181022095651.png)  
## 查看端口
当然，上述也有一定的局限性。可能出现的情况是，nginx进程还在，但是端口访问不了。所以，我们也可以从端口的角度出发查看服务是否正常运行。
```
netstat -tunlp |grep 443
```
输出如下：  
![](/assets/深度截图_选择区域_20181022100231.png)  
第一条是有监听的情况下，第二条是无监听的情况下的输出。  
这里就有两个处理的经验。如果端口有监听而时间上报链接拒绝，绝大多数情况都是防火墙没有开启。（关于防火墙的内容请自行百度）  
如果端口没有监听，我建议是杀掉所有相关进程进行重启。  
当然检查error.log是必要的。

## 进程管理器
在Linux下想要和Windows查看cpu、内存占用率的情况，可以使用top命令进行分析。自己敲一下吧，我不多讲了。


