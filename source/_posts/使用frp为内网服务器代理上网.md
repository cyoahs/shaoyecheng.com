---
title: 使用frp为内网服务器代理上网
date: 2021-05-28 23:20:28
tags:
- 工具
- 网络
---

frp是一个国人开发的非常易用的内网穿透工具，可以借助具有公网IP的服务器，建立从公网到内网的通道，并且[中文文档](https://gofrp.org/docs/)丰富，甚至可以在[github](https://github.com/fatedier/frp)上用中文提issue。

# frp基本结构

如下图所示，frp使用server/client的模式。一般来说，frps (server)运行在具有公网ip的服务器上，而frpc (client)运行在内网的服务器上。frpc连接到frps后，公网用户可以访问frps的ip与特定端口，访问对应的frpc。因此，抛开网络环境，使用frp最基础的条件就是，user与client都可以主动通过ip连接server。

![architecture](architecture.png)

# 需求

苦于浙大一个账号只能同时在线一台有线设备的设计，机房的服务器们都处于只能访问内网的状态。好在内网资源丰富，有镜像源，还可以直通清华、pythonhost等常用站，所以即使无法访问外网，也能满足大部分使用需求。这个问题可以简化为：**有AB两台电脑，A电脑无法上网，但是可以被B电脑通过内网ip访问，而B电脑可以访问公网，问如何使用A电脑访问公网？**

一些前辈们能够熟练地用ccproxy、squid之类的软件给服务器开代理，但是作者由于出生太晚，对“代理工具”的认识仅限于科学上网，并不了解前辈们的上古神器，就只能通过自己最熟悉的工具解决问题。

# 解决方案

由于frp的转发方向是user->frps->frpc->service，因此这里的A电脑同时作为user和frps，而B电脑作为frpc并建立service。

A电脑上配置文件最简单的形式为：

```
# frps.ini
[common]
bind_port = 7000
```

通过`./frps -c frps.ini`即可运行服务端。

B电脑上配置文件最简单的形式为：

```
# frpc.ini
[common]
server_addr = x.x.x.x # A电脑的IP地址
server_port = 7000 # frp连接端口，与frps配置相同

[http_proxy]
type = tcp
remote_port = 6000
plugin = http_proxy
```

通过`./frpc -c frpc.ini`即可运行服务端。

此时B电脑利用frp内置的http代理插件，建立了一个http代理服务，并且连接到了A电脑的frps。此时在A电脑上，就可以通过`A电脑IP:6000`，访问B上的http代理服务。只需要在命令行或浏览器等地方设置代理，就可以使用代理访问网络了。例如命令行中可以通过如下方式使用：

```
export ALL_PROXY=http://127.0.0.1:6000
curl www.baidu.com
```

如果需要访问不存在的网站，那么可以直接把转发服务指向自己的网上冲浪工具，在B电脑上配置文件如下

```
# frpc.ini
[common]
server_addr = x.x.x.x # A电脑的IP地址
server_port = 7000 # frp连接端口，与frps配置相同

[surfing]
type = tcp
local_ip = 127.0.0.1
local_port = 1080 # (网上冲浪端口)
remote_port = 6000
```

当然，如果A能直接通过IP访问B，那么直接打开B电脑网上冲浪工具的局域网共享功能即可。

上面的配置文件虽然可以直接运行，但是没有做任何安全性设置，并不是正确打开方式。~~（懂了，明天就去扫6000和7000端口）~~如果是长期使用，还是需要查看文档，注意安全措施。

# 和其他代理方法的区别

正如上文所说，老前辈们一般喜欢直接在B上用squit、ccproxy之类的开代理，网站管理员则喜欢用nginx、apach、caddy，而网上冲浪选手喜欢直接将冲浪工具的监听ip端放开。对于个人需求，最好的工具永远是自己最熟悉的工具。

如果真要找出frp相对于其他代理工具的优势的话，那就是frp只需要服务器有内网静态ip，而其他的代理工具需要个人电脑有内网静态ip。

# 小结

除了做代理之外，frp还可以干很多事情，例如~~规避最简单的url/ip审查、自建简单rvpn~~等。虽然user, server, client, service看似是四个对象，但实际上他们可以是任何数量的对象。