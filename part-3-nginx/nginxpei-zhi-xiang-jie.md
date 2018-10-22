
# nginx配置详解

## 结构
下面是nginx配置文件的结构
```
event{
  .....
}
http{
  upstream XXXXX{
    server XX.XX.XX.XX:XX;
  }
  server{
    listen XX；#监听的端口
    location XXXX{
      ....
    }
    ....
  }
  server{
    listen XX；
    location XXXX{
      ....
    }
    ....
  }
}
```
### http块中的upstream
```
upstream safeKeyBoard{
        server 10.79.0.239:8080;
        server 10.79.1.143:8080;
}
```
这个模块是用来包住ip，形成一个假的域名。这个将会在后面见到。  
当然还有情况是可以使用。就是可以做域名转发如
```
upstream lb  {
      server cache.opencfg.com weight=5;
      server app.opencfg.com max_fails=3  fail_timeout=30s;
}
```
## location表达式
location的配置有许多模式，简单罗列如下  
### 规则
|表达式|描述|
|---|---|
|~|#波浪线表示执行一个正则匹配，区分大小写|
|~*|#表示执行一个正则匹配，不区分大小写|
|^~|#^~表示普通字符匹配，如果该选项匹配，只匹配该选项，不匹配别的选项，一般用来匹配目录|
|=|#进行普通字符精确匹配|
|@|#"@" 定义一个命名的 location，使用在内部定向时，例如 error_page, try_files|
示例代码如下：
```
location = / {
 # 精确匹配 / ，主机名后面不能带任何字符串
 [ configuration A ] 
}
 
location / {
 # 因为所有的地址都以 / 开头，所以这条规则将匹配到所有请求
 # 但是正则和最长字符串会优先匹配
 [ configuration B ] 
}
 
location /documents/ {
 # 匹配任何以 /documents/ 开头的地址，匹配符合以后，还要继续往下搜索
 # 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
 [ configuration C ] 
}
 
location ~ /documents/Abc {
 # 匹配任何以 /documents/ 开头的地址，匹配符合以后，还要继续往下搜索
 # 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
 [ configuration CC ] 
}
 
location ^~ /images/ {
 # 匹配任何以 /images/ 开头的地址，匹配符合以后，停止往下搜索正则，采用这一条。
 [ configuration D ] 
}
 
location ~* \.(gif|jpg|jpeg)$ {
 # 匹配所有以 gif,jpg或jpeg 结尾的请求
 # 然而，所有请求 /images/ 下的图片会被 config D 处理，因为 ^~ 到达不了这一条正则
 [ configuration E ] 
}
 
location /images/ {
 # 字符匹配到 /images/，继续往下，会发现 ^~ 存在
 [ configuration F ] 
}
 
location /images/abc {
 # 最长字符匹配到 /images/abc，继续往下，会发现 ^~ 存在
 # F与G的放置顺序是没有关系的
 [ configuration G ] 
}
 
location ~ /images/abc/ {
 # 只有去掉 config D 才有效：先最长匹配 config G 开头的地址，继续往下搜索，匹配到这一条正则，采用
  [ configuration H ] 
}
```

顺序 no优先级：
(location =) > (location 完整路径) > (location ^~ 路径) > (location ~,~* 正则顺序) > (location 部分起始路径) > (/)

上面的匹配结果  
按照上面的location写法，以下的匹配示例成立：

/ -> config A：精确完全匹配，即使/index.html也匹配不了
/downloads/download.html -> config B：匹配B以后，往下没有任何匹配，采用B  
/images/1.gif -> configuration D：匹配到F，往下匹配到D，停止往下  
/images/abc/def -> config D：最长匹配到G，往下匹配D，停止往下。你可以看到 任何以/images/开头的都会匹配到D并停止，FG写在这里是没有任何意义的，H是永远轮不到的，这里只是为了说明匹配顺序  
/documents/document.html -> config C：匹配到C，往下没有任何匹配，采用C  
/documents/1.jpg -> configuration E：匹配到C，往下正则匹配到E  
/documents/Abc.jpg -> config CC：最长匹配到C，往下正则顺序匹配到CC，不会往下到E  
（此段转自网络）
### 实际使用的建议

1.根目录/建议使用“=”
2.静态目录的分发建议使用^~
3.一般的情况下，大型的应用，h5、app、小程序端之后是会有一个专用的中台。所以，接口的转发最好的是有上下文进行分割、然后用～*匹配路径。若有例外则使用^~覆盖～*的配置。
4.在没有把握的情况下，尽量使用～*然后放在偏后的位置。

## 重定向
重定向分为错误重定向和直接重定向。
例如：首页的重定向
```
location =/{
    rewrite ^/ http://$host:$server_port/index.html#index/;
}
```
