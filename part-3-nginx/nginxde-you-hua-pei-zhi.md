# nginx 优化配置

## 导读

上一篇仅仅是为了用起来，这一篇是为了用得好。本文提到的配置或多或少的都应用到了我们的服务器上。  
本文默认的环境是 **linux**

## 隐藏版本号

### 必要性

软件的版本是不停迭代的，而我们为了稳定不得不一直采用一个版本。万一这个时候nginx官方公布了一个漏洞，而这个漏洞是针对我们这个版本的。那就很容易被别有用心的人利用。  
所以隐藏版本号是一项治标（不治本）的方案。  
目前触屏所有服务器已采用。

### 实施过程

1. 在nginx的conf文件中添加这一行。
   ```
   server_tokens off;#隐藏版本号
   ```
2. 修改fastcgi.conf文件
   将fastcgi\_param  SERVER\_SOFTWARE一栏修改为 nginx/;
   保存并退出
3. 采取killall nginx的方式硬重启nginx

### 验证方式

chrome中可以看到，版本号已经不见了  
![](/assets/深度截图_选择区域_20181022152359.png)

## 无缓存机制

### 必要性

这个其实很简单，就是让对方的浏览器不长时间保留缓存。这个针对微信浏览器，因为微信带有较长时间的cookie，导致我们的页面一直没有更新。所以，就要告诉浏览器这个页面是新鲜的，不能保存的。  


### 实施过程

在需要的location添加这一段话

```
        add_header Cache-Control "private, no-store, no-cache, must-revalidate, proxy-revalidate";
```

（重启nginx，软重启即可）

### 验证方式

chrome查看缓存控制  
![](/assets/深度截图_选择区域_20181022152551.png)

### 注意事项

坚决不建议放在server块或者html块，这样会大幅降低再次浏览速度。

## Http2

### 必要性

新一代http协议，可以快速便捷有效的进行访问，目前最大的问题是pc端的兼容性问题。

### 实施过程

1. 确保nginx安装的版本在1.10.X以上，如果太老旧的版本需要切换
2. 重新编译！！！（也即重新安装）  
   那么如何编译呢？这就要提到我们从零开始部署服务器的里面经常提到的安装三部曲
   ```
   ./configure
   make
   make install
   ```

   但是config需要带上其他各种各样的选项，这里我们带上--with-http\_v2\_module ，再make和make install（称为重新编译）即可  
   **请看注意事项**
3. 这个时候，我们还需要修改配置。  
   443端口的配置如下。

   ```
   server {
    listen       443 ssl http2;
    server_name  localhost;

    ssl_certificate     server.crt;
    ssl_certificate_key  server.key;
   }
   ```

   这样就能应用成功了

### 注意事项

1. 请config的时候带上已经编译过的选项！！！别只顾着装http2之后忘记了其他本来需要依赖的模块。那么怎么看呢？进入你nginx目录执行nginx -V（注意是大写），查看当前编译的配置。  
   生产上由于应用了openresty所以来得更加复杂。  
   ![](/assets/深度截图_选择区域_20181022152732.png)
   看到绝望怎么写没有  
   不过，我可以说，只有
   ```
   --with-http_realip_module --with-pcre=/opt/tools/nginx_install/pcre-8.36 --with-http_ssl_module --with-openssl=/opt/tools/nginx_install/openssl-1.0.2d --with-zlib=/opt/tools/nginx_install/zlib-1.2.8 --with-http_stub_status_module --with-threads --add-module=/opt/tools/nginx_install/openresty-1.11.2.2/bundle/nginx_upstream_check_module-0.3.0 --add-module=/opt/tools/nginx_install/openresty-1.11.2.2/bundle/ngx_cache_purge-2.3 --with-stream --with-stream_ssl_module --with-http_v2_module --with-http_gzip_static_module
   ```

   这个是需要configure的。自己想想为什么。
2. http2必须采取在443端口上用https协议传输的。具体为什么，就不多讲了。如果是生产的服务器，里面本来就有安全证书了。如果是自己本地的测试环境，还需要给自己签发安全证书。
   命令如下
   ```Bash
   openssl genrsa -des3 -out server.key 1024
   cp server.key server.key.org
   openssl rsa -in server.key.org -out server.key
   openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
   ```

   然后你访问的时候会弹出不安全。
   这不是废话么，你算那根毛   
   ![](/assets/20171212-9b0511e2.png)   

你自己签发的安全证书不是安全机构颁发的，自然会红，无脑信任就可以了。

## gzip压缩

### 必要性

小伙子，听说过安利不嘞。  
不不不，走错片场了。听说过打包传输没有....没有？没有我不打包了，我打你  
![](/assets/20171212-ed4e455a.png)  
简单粗暴的理解就是先压缩再传就快，大幅提高浏览体验。不过这里又分动态压缩和静态压缩。  
目前ss系统已经全面采用静态压缩技术

#### 动态压缩

动态压缩简单粗暴，有序高效，基本上除了ie的兼容性外几乎没有坑。但是因为动态压缩对服务器性能消耗较大，所以目前存有明显流量峰值的服务器（例如会员日）请谨慎使用。

#### 静态压缩

静态压缩是在动态压缩的基础上继续修改而成的一个内容，预先生成gz压缩包。nginx在 **有压缩包的情况下优先获取压缩包**，无压缩包再传输二进制页面。  
这样做避免消耗服务器的资源用于压缩，又可以传少一点东西，很明显是一个最佳的方案。  
那么缺点，就是部署起来相当的麻烦。

### 实施过程

#### 动态压缩

动态压缩简单粗暴，只需要在server块里面填写

```
# 开启gzip
gzip on;
# 启用gzip压缩的最小文件，小于设置值的文件将不会压缩
gzip_min_length 1k;
# gzip 压缩级别，1-10，数字越大压缩的越好，也越占用CPU时间，后面会有详细说明
gzip_comp_level 2;
# 进行压缩的文件类型。javascript有多种形式。其中的值可以在 mime.types 文件中找到。
#gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
# 是否在http header中添加Vary: Accept-Encoding，建议开启
gzip_vary on;
# 禁用IE 6 gzip
gzip_disable "MSIE [1-6]\.";
```

就可以了。

#### 静态压缩

1. 先将nginx重新编译，带上编译选项--with-http\_gzip\_static\_module
2. 修改配置文件，加上
   ```
   #启动预压缩功能，对所有类型的文件都有效  
   gzip_static on;
   #找不到预压缩文件，进行动态压缩
   gzip on;
   #是否在http header中添加Vary: Accept-Encoding，建议开启
   gzip_vary on;
   ```
3. 把文件夹中所有需要压缩传输的文件都生成以该文件名命名的gz包，例如index.html则生成index.html.gz
4. 这样你一个个压，那就会把你压成可达压。  
   ![](/assets/20171214-fa3c5c82.png)
5. 这里就用一下脚本，这个脚本如下（这个脚本看不懂不要问我，很难讲懂给你听）

```bash
ulimit -n 10000 #修改系统默认进程最大使用的文件数，否则此脚本会报错
# find ./ -type f -printf '%TY %Tm %Td %TH %TM %TS\t%p\n' 寻找本目录下所有文件
# grep -iE '\.(html|txt|css|js)$' 寻找所有以html等结尾的文件
# awk -F'\t' 以回车（\t）作为分隔逐行执行命令
find ./ -type f -printf '%TY %Tm %Td %TH %TM %TS\t%p\n'| grep -iE '\.(html|txt|css|js|json)$'| awk -F'\t' '{
# $2是awk一行输出的第二个参数，也即文件夹和路径
gz_file=$2 ".gz";
stat_cmd="stat -c \"%Y\" " gz_file; #获取时间戳的命令
exist_cmd="[  -e \"" gz_file "\" ] && echo 1 || echo 0"; #查看gz文件是否存在的命令，存在输出1否则输出0

exist_cmd | getline gz_file_exist; #检测是否存在，并将状态存储gz_file_exist

if (gz_file_exist) {
    stat_cmd | getline last_modify_timestamp; #获取时间戳
} else {
    last_modify_timestamp=0;#说明是新建的gz压缩文件
}
# 若现存文件更新则更替
if (last_modify_timestamp < mktime($1)) {
    system("gzip -c9 " $2 " > " gz_file); #启用压缩命令
    print gz_file " [created]"; #打印文件创建
}

}'
```

### 注意事项

1. ulimit语句用于突破linux中单个程序不可操作超过1024个文件的限制，
2. **这条很重要，在增量发布的服务器上，代码的发布是有不同的就盖上去，没有不同的就保留。万一你的压缩包没有生成成功，那他还是优先读取服务器中存留的gz包，这个时候相当于你没发布代码。**

## 强转化https

### 必要性

不知道为什么，反正就是要加强安全性，统一强制要求跳转https，防止http被窃听。

### 实施过程

```
server {
    listen       80 ;
    server_name  XXXXXXXX;
    #保留健康检测地址
    location  /monitor {
        default_type text/html;
        return 200 'http status 200';
        access_log logs/f5-monitor.log main;
    }
    #其他location
    #其余的一律专跳成https
   location  / {
        rewrite ^(.*)$  https://$host$1 permanent;  
    }     
}
```

### 注意事项

暂无

## combo

### 必要性

主要是解决多个包传输的速度慢的问题。  
我们服务器最多允许同时进行四条通信，但是我们的首页少说也十几个包吧。这个时候就很麻烦了，因为需要不停的去调用http协议，这样会比较慢，所以减少包的数量也是会提高速度。

### 实施方案

传送门[https://github.com/alibaba/nginx-http-concat](https://github.com/alibaba/nginx-http-concat)  
这个网站已经详尽的记录了如何使用这个阿里巴巴出品的开源插件。  
简略说明如下：  
1. 安装并编译，这个注意事项和前面的需要编译的都是一致的  
1. 在需要的上下文中配上 concat on;选项  
1. 在需要使用combo的包上配置类似

```html
<link rel="stylesheet" href="??foo1.css,foo2.css,subdir/foo3.css?v=2345" />
```

这样的格式

### 验证方案

因为代码改了，所以如果没有发生意外，那么就意味着真的是应用成功了。

### 注意事项

这个必须是同类文件，不能js和css混合成一个combo。

