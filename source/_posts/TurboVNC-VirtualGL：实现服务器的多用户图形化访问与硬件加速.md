---
title: TurboVNC+VirtualGL：实现服务器的多用户图形化访问与硬件加速
date: 2020-04-08 13:26:13
tags: 工具
---
作为一个日常宅家/移动办公的科研狗，远程连接高性能服务器是居家旅行的基本操作。有了配置好的服务器，就可以在世界上任何一个能上网的角落愉快地科研。笔者也一直使用六年前的macbook pro和三年前的台式机(i5-6500+核显)，远程操作学校里的服务器(两颗e5-2696v4，四块GTX1080Ti)进行科研工作。

远程操作服务器(linux系统)的方法主要有ssh，vnc和teamviewer，Windows系统还可以使用Windows自带的rdp服务。ssh主要使用命令行操作，虽然命令行确实可以完成大部分工作，熟练掌握命令之后可以获得geek的成就感，但是命令行的学习曲线比较陡峭，图形界面也能给我们带来很大的便利。

vnc和teamviewer可以直接提供图形化的访问方式，十分便捷，两种方式的基本原理都是截图-压缩-发送。对于没有固定ip且服务器可以上网的情况，teamviewer是一个非常好用的工具，能够teamviewer所提供的id直接实现两台电脑的通信。

但是笔者有一个神奇的需求，服务器没有显示屏输出，同时又有多人同时用图形界面访问，并且需要使用OpenGL。很多软件都无法满足这么神奇的需求，但最后找到了TurboVNC+VirtualGL的解决方案。

# 太长不看版

| 多人同时使用OpenGL应用 | GPU硬件加速 | 解决方案             |
| ---------------------- | ----------- | -------------------- |
| 需要                   | 不需要      | TurboVNC only        |
| 需要                   | 需要        | TurboVNC + VirtualGL |

只推荐机房服务器使用VirtualGL，因为从本地直接登录安装了VirtualGL的服务器可能会引起VirtualGL不稳定，只适合远程使用。

# 普通VNC以及存在的问题

普通的vnc软件主要存在两个问题，一个是多人访问问题，另一个是OpenGL硬件加速问题。

Linux的图形化界面通过X-window实现，当多用户同时使用图形化的方式访问各自的内容时，工作原理类似于给X-window设置多个显示器，每个用户都只看到自己的显示器。这时，vnc就会在X-server中抓取某个“显示器”的内容，压缩发送给特定用户。但是在使用OpenGL硬件加速时，GPU渲染的画面会绕过X-server进行输出，此时vnc就无法“看到”这些3d画面。

当只有一个用户使用时，vnc软件会尝试从显存里面抓画面，弥补X-server中截图丢失的内容。但是多个用户同时使用时，他们都在用同一个缓存，软件区分不了谁是谁。所以对这些软件来说，多用户和硬件加速两个要素，就像鱼和熊掌。

# VirtualGL与TurboVNC解决方案

VirtualGL的原理相当于创建一个虚拟的显卡，抓取应用软件的渲染指令，转发给GPU渲染，再将GPU输出的图像输出给X-server。这样，vnc就能愉快地从X-server拿到完整的截图。由于又是X-server级别的图像抓取，不存在多用户的问题。

当然普通的vnc都有各自奇奇怪怪的OpenGL解决方案，所以VirtualGL项目有专门做了一个亲生的vnc软件，TurboVNC。TurboVNC主打性能，号称他们神奇的算法可以在高延迟低带宽的场景下提供流畅的体验。当然他们的重点是可以和自家的VirtualGL一起用。

# 配置方法

## 下载并配置VirtualGL

从Source Forge下载最新版本对应的安装包

```
https://sourceforge.net/projects/virtualgl/files/
```

ubuntu系统推荐使用deb安装包

![fig1](fig1.png)

参照官网文档，安装VirtualGL。官网建议在安装显卡驱动之前安装VirtualGL，否则OpenGL库的符号链接可能会出现一些问题。

```bash
# https://virtualgl.org/Documentation/Documentation
# 可能需要sudo权限

dpkg -i virtualgl*.deb
apt install -f 
```

对于笔者这种没有显示器的神奇需求，需要先在显卡上创建一个没有显示器的X-window，有显示器可以忽略这一步。

```bash
# https://virtualgl.org/Documentation/HeadlessNV

# 查看显卡的车牌号(划掉，busid)
nvidia-xconfig --query-gpu-info
# 在对应的显卡上创建X-window
sudo nvidia-xconfig -a --allow-empty-initial-configuration --use-display-device=None
--virtual=1920x1080 --busid {busid}
```

配置从VirtualGL启动3D X-server，这个过程中需要关闭桌面环境

```bash
service gdm stop
```

如果在本地操作，这时屏幕会进入黑屏，因为图形化的界面已经被关闭了，按ctrl+alt+f1进入命令行界面，配置VirtualGL。

```bash
/opt/VirtualGL/bin/vglserver_config
选择 1 configure server for use with VirtualGL in GLX mode
```

途中需要选择一些选项，全部选No即可。安装完成后，重新启动桌面环境

```bash
service gdm start
```

这时按ctrl+alt+f7就可以重新切换到桌面环境。进入终端，测试安装是否正确

```
xdpyinfo -display :0
/opt/VirtualGL/bin/glxinfo -display :0 -c
```

正常情况下可以看到如下内容

![fig2](fig2.png)

如果出现问题，可以删除当前配置

```bash
/opt/VirtualGL/bin/vglserver_config
选择 2 Unconfigure server for use with VirtualGL in GLX mode
```

## 配置TurboVNC

同样从Source Forge下载对应版本的安装包

```
https://sourceforge.net/projects/turbovnc/files
```

参照官网文档，使用dpkg安装TurboVNC

```bash
# https://turbovnc.org/Documentation/Documentation

dpkg -i turbovnc*.deb
```

启动服务，首次启动时需要设置密码

```bash
/opt/TurboVNC/bin/vncserver
```

启动成功后，可以得到如下结果。注意记下最后一个数字。当开启多个Turbovnc服务时，使用最后的数字区分不同屏幕。

```bash
Desktop 'TurboVNC: XmechGPU:1 (my_user)' started on display XmechGPU:1
```

启动成功后，除非使用下方命令手动关闭，服务一直会运行。其中n为上面的数字。

```
/opt/TurboVNC/bin/vncserver -kill :n
```

## 从windows或mac远程访问服务器

回到Source Forge，下载符合自己系统版本的TurboVNC，mac的版本需要单独安装jdk。

```
https://sourceforge.net/projects/turbovnc/files
```

连接内网，输入服务器ip以及上面获得的vnc编号，验证密码即可登录服务器

![fig3](fig3.png)

TurboVNC内置了CPU软件实现的OpenGL，可以直接运行OpenGL的程序，但背后是软件渲染。例如使用glxgears进行测试，可以看到旋转的齿轮。

![fig4](fig4.png)

如果使用硬件加速，需要在原本的命令前加上vglrun。此时画面会使用GPU进行硬件加速，齿轮的帧速就会大幅提高。

![fig5](fig5.png)

如果服务器安装了anaconda，可能会产生黑屏，需要将anaconda的路径从PATH中移除，并重新启动vncserver，就能恢复正常。当vncserver正常运行时，环境变量就不会再影响vnc，可以正常使用anaconda。

# 小结

如果只需要使用OpenGL，而对性能要求不高，可以直接安装TurboVNC，内置的软件渲染可以基本满足要求。但是如果是比较复杂的3d软件，则需要另外安装VirtualGL。

最后也贴一下自己的配置，1080Ti感受到了贫穷。

![fig6](fig6.png)