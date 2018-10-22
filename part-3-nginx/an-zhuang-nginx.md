# nginx安装

## 步骤1 ：下载源码包 {#-1-}

地址：

1. [http://nginx.org/download/](http://nginx.org/download/)
2. 解压

   ```
   tar -xzvf nginx-XXX
   ```

3. 先别急
4. 我们安装一下依赖包

```
 yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel pcre-devel

```

   然后再进行三部曲
   
```
./configure  --with-http_stub_status_module --with-http_ssl_module --with-pcre --with-stream --with-stream_ssl_module  --with-threads 
#若需要安装多个nginx请添加 --prefix=/usr/local/nginx2   
若需要采用http2 --with-http_v2_module 需要gzip静态压缩技术则 --with-http_gzip_static_module 选项
make
make install
```

## 步骤2：验证

  1.启动，然后开放防火墙的80端口
  2.成功访问
  
  ![](/assets/深度截图_选择区域_20181022110002.png)

## 注意事项：
本文的环境是Redhat6.5，在Ubuntu上的依赖命令请自行探索。
而Windows上nginx的安装更为简单。  
只需要把压缩包解开，然后点击nginx.exe即可。  
需要重启（关闭）时，只需要按着shift+右键，打开命令行，输入nginx -s reload（stop）  
此外，Windows环境下多次启动不会报错，造成僵尸进程。所以请确定你的nginx没有启动才双击。




