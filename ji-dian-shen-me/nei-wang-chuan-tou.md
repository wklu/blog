# 内网穿透



## 1.使用frp进行内网穿透

@\[toc\]

### 1.1.我们能用frp干什么？

1. 在具有公网IP地址的服务器上，将NAT或防火墙后面的任何tcp或udp服务暴露给互联网。
2. 通过具有公共IP地址（基于名称的虚拟主机支持）的服务器将NAT或防火墙后面的任何http和https服务暴露给互联网。

### 1.2.通过SSH访问LAN中的计算机

#### 1.2.1.下载软件

* 软件：frp\_0.12.0\_linux\_amd64.tar.gz [点击下载](https://github.com/fatedier/frp/releases/download/v0.12.0/frp_0.12.0_linux_amd64.tar.gz)

  > * wget： wget [https://github.com/fatedier/frp/releases/download/v0.12.0/frp\_0.12.0\_linux\_amd64.tar.gz](https://github.com/fatedier/frp/releases/download/v0.12.0/frp_0.12.0_linux_amd64.tar.gz)
  >
  > 因为网络问题，推荐本地方式下载，通过FTP上传到服务器端再解压
  >
  > 说明：
  >
  > ```text
  > 坏境准备:一台有公网IP的服务器作为服务端，只有内网的机器作为客户端
  > ```

#### 1.2.2.解压软件

```text
命令：tar -zxvf frp_0.12.0_linux_amd64.tar.gz
```

#### 1.2.3.解压后目录如下：

* frpc 客户端软件
* frpc\_full.ini  
* frpc.ini  客户端配置文件
* frps  服务端软件
* frps\_full.ini  
* frps.ini  服务端配置文件
* LICENSE

```text
命令：cd frp*
```

#### 1.2.4.修改服务器端配置

```text
命令1：vi frps.ini
＃ frps.ini 
[common] 
bind_port = 7000

命令2(启动服务)：./frps -c ./frps.ini
```

#### 1.2.5.修改客户端配置

```text
命令1：vi frpc.ini
[common]
server_addr = 112.*.*.*
server_port = 7000

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000

命令2（启动服务）：./frpc -c ./frpc.ini
```

说明:

* server\_addr 公网IP
* server\_port 服务器端口
* local\_port  本地要替换的端口
* remote\_port  替换端口（ssh连接时候改成这个端口）

> OK,快用Xshell连接试试看吧！ ssh -oPort=6000 **root@x.x.x.x**

### 1.3.frp内网穿透实战

> 说一千，道一万，不如撸起袖子干一干
>
> * 场景：有公网IP得阿里云服务器一台 A
> * 只有内网联想服务器主机 B

#### 1.3.1.B服务器配置

frpc.ini：

`[common]`

`server_addr = 114.xx.xx.xxx`

`server_port = 7000`

`[ssh]` 

`type = tcp` 

`local_ip = 127.0.0.1` 

`local_port = 22` 

`remote_port = 6000`

#### 1.3.2：客户端配置



`[common]` 

`bind_port = 7000`

`[mysql]` 

`type = tcp`

 `local_port = 3306` 

`remote_port = 3306`

`use_encryption = true`

`use_compression = true`

`[tomcat]`

`type = http`

`local_port = 8083`

`custom_domains = tomcat.xxxx1.com`

`remote_port =`

`[web]`

`type = http`

`local_port = 8083`

`subdomain = tomcat`

#### 1.3.3.通过二级域名管理tomcat

`[web01]` 

`type=http local_ip = 127.0.0.1` 

`local_port = 8083` 

`custom_domains = tomcat.xxxx1.com` 

`localhost = /`

#### 1.3.4.通过二级域名管理nexus

`[web02]`

 `type=http` 

`local_ip = 127.0.0.1` 

`local_port = 8084` 

`custom_domains = nexus.xxxx1.com` 

`localhost = /`

## privilege\_allow\_ports = 8082,8083

vhost\_http\_port = 80 vhost\_https\_port = 5051

## subdomain\_host = xxxx1.com

## subdomain\_host = xxxx2.com

## dashboard\_port = 7500

## 仪表板的用户名和密码都是可选的，如果没有设置，默认是admin。

## dashboard\_user = admin

## dashboard\_pwd = admin

```text
- 执行
```

setsid ./frps -c ./frps.ini

```text
- frpc.ini
> 将请求域名作转发处理，因为vhost_http_port=80占用了80端口，导致生产环境上得网站不能直接通过域名来访问，如果vhost_http_port不等于80则导致访问通过子域名访问内网环境都需要在url后面加上81端口。
```

\[common\] server\_addr = 127.0.0.1 server\_port = 7000

## \[ssh\]

## type = tcp

## local\_ip = 127.0.0.1

## local\_port = 22

## remote\_port = 6000

\[web\] type = http local\_port = 81 custom\_domains = www.xxxx.com \[linkai\] type = http local\_port = 82 custom\_domains = www.xxxx1.om \[luming\] type = http local\_port = 83 custom\_domains = www.xxxx.net

```text
- 执行
```

setsid ./frpc -c ./frpc.ini

```text
- 杀掉进程
```

命令1：ps -ef \|grep frpc.ini\(或frps.ini\) 命令2：kill -9 xxxx

\`\`\`

> **实战总结** 1. 如果要使用二级域名形式必须得设置了\*解析，或者你要得二级域名解析 2. 服务端必须设置 vhost\_http\_port = 80 才能直接通过配置域名访问 3. 服务端配置vhost\_http\_port 不为80时候，访问路径需要加上这个替换端口 4. 如果设置了privilege\_allow\_ports，需要注意白名单范围，不在范围内得端口不能访问 5. setsid\(\)调用成功后，进程成为新的会话组长和新的进程组长，并与原来的登录会话和进程组脱离。由于会话过程对控制终端的独占性，进程同时与控制终端脱离。 6. subdomain\_host 设置后，无法是使用custom\_domains
>
> 能力有限，还请多多指教

