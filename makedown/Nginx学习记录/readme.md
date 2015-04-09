# 欢迎阅读Nginx分享
----

## Nginx简介

#### Nginx是什么？

> Nginx是`engine	['endʒɪn] X`的简写，是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，并在一个BSD-like 协议下发行。由俄罗斯的程序设计师Igor Sysoev所开发，供俄国大型的入口网站及搜索引擎Rambler（俄文：Рамблер）使用。其特点是占有内存少，并发能力强，是目前市面上唯一能和kangleweb server比拼的web server，事实上nginx的并发能力确实在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：新浪、网易、腾讯等。[详情猛戳这里](http://baike.baidu.com/view/926025.htm?fr=aladdin)

###### 总结：
- 定义： 轻量级、高性能、开源的web服务器 
- 主要功能：Http服务器、反向代理服务器、邮件服务器
- 为什么用它：
更快、 高扩展性、 高可靠性低内存消耗、
单机支持10万以上的并发连接、
热部署、最自由的BSD许可协议
- 谁在用Nginx：<br />
  *国外* **ordpress Sourceforge Github Hulu ...**
  *国内* **新浪 网易 搜狐 校内 迅雷 CSDN...**

#### 作者的美貌？
![Alt text](./Igor-Sysoev.jpg)
###### 大名： Igor Sysoev 伊戈尔 塞索耶夫

----

## 下面介绍在Mac下安装和部署Nginx

#### 获取Nginx安装包
- 我们先去官网下载最新的稳定版的Nginx包，然后将其解压放到指定目录，我的机子解压后放在了/Applications/DEV/myNginx/source目录。
![Alt text](./nginx-down.png)
图1：Nginx下载界面（[立即下载](http://nginx.org/download/nginx-1.6.2.tar.gz)）

- 在这个目录下将其解压
![Alt text](./tar-nginx.png)
图2：解压Nginx

- 解压后的Nginx的目录结构如下
![Alt text](./nginx-dir.png)
图3：Nginx目录结构

#### Nginx安装步骤
- 打开终端，进入Nginx解压目录
`cd /Applications/DEV/myNginx/source/nginx-1.6.2/`

- 执行命令：`./configure prefix = 你的nginx准备安装的目录`
这个命令会配置nginx的安装路径，./configure表示在当前这个路径下找configure这个命令来执行，我的nginx准备安装在/Applications/DEV/myNginx/install下面，所以我的命令是：` ./configure prefix=/Applications/DEV/myNginx/install`
![Alt text](./config-nginx.png)
图4：配置nginx安装路径

- 现在我们用命令进入nginx解压后的文件夹里的 `objs` 目录下，
 使用vi命令修改修改该目录下的 **Makefile** 文件（）：`vi Makefile`, 然后按字母 `i` 进入该文件的编辑模式，按上下左右移动光标去需要改动的代码位置，删除文件文本内容 `-Werror`，删除修改后按 **esc** 键返回vi编辑器的命令模式，敲击 `:wq!` 命令保存修改并退出。（[vi编辑器详解猛戳这里](http://baike.baidu.com/view/908054.htm?fr=aladdin)）
 ```
 cd /Applications/DEV/myNginx/source/nginx-1.6.2/objs
 vi Makefile 
 ```
![Alt text](./werror.png)
图5：要去除的-Werror文本

- 修改了Makefile进入nginx解压后的根目录，在这个路径下执行 `make` 命令，等make命令执行后相关信息输出完毕再执行命令：` make install` 命令，这样我们的Nginx就安装成功了。
 ```
cd /Applications/DEV/myNginx/source/nginx-1.6.2/
make
make install
```
![Alt text](./make.png)
图6：进入nginx解压根目录执行make命令
![Alt text](./make-install.png)
图7：执行make install命令安装nginx

- 安装nginx成功之后会在图4中prefix值所示的目录中生成安装后的文件
![Alt text](./install-success.png)
图8：nginx安装后生成的文件

##### 至此，nginx就已经安装完成了... 如果你按照上面的步骤成功安装nginx，那么，恭喜发财，如果你还在痛苦着，那么你也不会快乐着，继续装吧！

####启动和访问nginx首页
- 启动nginx
 进入nginx安装目录的sbin目录下，执行该目录下的nginx命令文件即可。
 
 ```
cd /Applications/DEV/myNginx/install/sbin
./nginx
 ```
![Alt text](./start-error.png)
图9：启动nginx报错界面
**注意 ：** 
> 这里启动报错了是因为nginx的默认端口是80端口，mac下80端口需要更高的用户权限才能使用（1000以下的端口号需要系统权限），如果不改，nginx就不能启动，所以这里可以自己随意改个端口号为8181。

- 修改nginx端口号
进入nginx安装目录下的conf文件夹，用vi编辑器的编辑命令修改这里的nginx.conf配置文件，将里面的代码段： 
   `server { listen 80; server_name localhost;` 
   中的80改成8181，这里的listen就是配置nginx访问端口的。
   ```
  cd /Applications/DEV/myNginx/install/conf 
  vi nginx.conf
   ```
![Alt text](./port.png)
图10：nginx默认80端口
 ![Alt text](./8181.png)
图11：nginx端口改成8181
>接下来再次进入nginx安装目录的sbin目录下，执行下面的命令重新加载一下nginx的配置即可以最新的配置重启nginx。
 ```shell
 cd /Applications/DEV/myNginx/install/sbin
./nginx -s reload
 ```
 
- 访问nginx首页测试nginx是否启动成功
打开浏览器，输入地址`http://localhost:8181/`,如果成功看见nginx的欢迎页面，则表示安装-配置-启动nginx一套拳全部打完。
 ![Alt text](./end.png)

----

## 经过上面的步骤，Nginx已经成为了mac的一部分了。由于nginx篇幅太多，无法细说，所以大致给大家介绍下他的静态资源服务器和反向代理部分内容。

###介绍之前，先介绍一下nginx的配置文件：nginx.conf。这是一个普通的文本文件，nginx的功能主要都是靠修改它的配置来完成.

>  nginx.conf文件内容：
 <pre>worker_processes  1; 
events {
	 worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen       8181;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
} </pre>
上面是nginx.conf里的内容，event、http、server、location等都称为块配置项。

- 用HTTP模块块配置一个静态Web服务器，包括了http块，server块、location等。
 >server：一个server就是一个虚拟主机，他只处理与之相对应的主机域名请求。
location：他会尝试根据用户请求中的URI（统一资源标识符（Uniform Resource Identifier，或URI)是一个用于标识某一互联网资源名称的字符串	）匹配location值的/uri表达式，如果可以匹配，就选择location{	}块中的配置来处理用户请求。<br />
server里面可以配置多个location，用于匹配地址，location根据用户请求地址中的URI来匹配的，匹配到了，用location{}块中的配置来处理用户请求。
location是顺序匹配的，一个请求命中多个location，请求只会被第一个匹配上的location处理。
如果地址栏的匹配在显示location配置中都没有则会默认在html下面找，找到返回，找不到最终报错。比如 http://localhost:9999/index.html，最终在html下面是有的，如果之前的location匹配到了，则用匹配到的location返回结果。

1. 通过nginx访问静态资源
> 只需要修改location的配置就可以通过nginx访问静态资源，location的值用于匹配用户的请求，那么nginx响应什么就需要下面介绍的文件路径定义的配置项了。
>
><pre>
>（1） root方式设置资源路径
第一种：
location / {
            root   mypage;
            index  default.html default.htm;
}
**这种情况nginx实际找资源的路径：nginx安装根目录/mynginx/goods/**
一般如果配置了多个location，location / {	}放在最后，表示当其他的location都失败后，由它来处理。 / 可以匹配到所有nginx请求，根据请求地址的uri部分到root值下面找，找到就返回，找不到报错，如果地址栏没有uri部分，最终匹配到/就结束了，则在root中指定目录找index中指定的页面响应，如果有页面招不到报错信息可以去logs下面的文件中看。mypage目录都是在nginx安装根目录下面。
http://localhost:8181/ (访问index默认页)
http://localhost:8181/facebook.html （访问制定的facebook.html页面）
第二种：
location /goods/ {
			root /mypage/;
			index default.html;
}
**这种情况nginx实际找资源的路径：/mynginx/goods/**
地址 http://localhost:8181/goods/goods-index.html
这种方式实际测试访问资源地址： /mynginx/goods/goods-index.html
location一旦加上地址，nginx实际查找文件的资源路径不再是相对于nginx的绝对路径：nginx安装目录/mynginx/goods/goods-index.html，而是以root配置的路径来做绝对路径访问，所以location /goods/的这种情况下，root后面不能再跟相对路径了。
第三种：
location /goods/ {
	   root /Applications/DEV/myNginx/install/mypage/;
       index default.html;
}
**这种情况nginx实际找资源的路径是/Applications/DEV/myNginx/install/mypage/goods/**
http://localhost:8181/goods/  匹配到location，但是没有访问文件，所以用默认页面default.html返回。
http://localhost:8181/goods/goods-index.html 匹配到真实页面返回
http://localhost:8181/goods/buy.html匹配到location，但是没有buy.html ，报错
</pre>
><pre>
>（2）以alias方式设置资源路径
>第一种：
>location /findLeader {
			alias /Applications/DEV/myNginx/install;
			index notG.html;
}
**这种情况nginx实际找资源的路径是/Applications/DEV/myNginx/install/uri部分**
地址栏输入：
http://localhost:8181/findLeader/wandouLeader/taG.html
则会把findLeader后面的部分陪拼接到alias值后面去查找资源文件。
地址栏输入：
http://localhost:8181/findLeader/wandouLeader/
因为没有指定要什么资源，所以用index配置的默认页面notG.html返回给用户，如果index的notG.html不存在，则报错。
第二种：
location /findLeader {
			alias /Applications/DEV/myNginx/install;
			autoindex on; 
			index notG.html;
}
**多了一个autoindex on，这种情况nginx实际找资源的路径是/Applications/DEV/myNginx/install/uri部分**
地址栏输入：http://localhost:8181/findLeader/，会列出alias配置的资源路径下的所有文件夹列表。
地址栏输入：http://localhost:8181/findLeader/wandouLeader/，会展示index配置的默认页面。
如果注释掉	index notG.html，浏览器输入下面地址，
http://localhost:8181/findLeader/wandouLeader/
这样就不会返回默认页面了，但是不会报错，会列出alias地址+wandouLeader下面的目录列表。
最后，如果地址栏输入的请求地址在nginx中一个location都没有命令，比如：
http://localhost:8181/findMoney/moneyList.html
则默认去nginx安装根目录下/html/findMoney/moneyList.html查找文件，招不到会报错。
Nginx中所有的错误信息都可以在Nginx安装目录下的logs下的error.log文件中查找错误日志。
></pre>