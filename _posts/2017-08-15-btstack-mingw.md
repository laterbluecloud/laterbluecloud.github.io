---
layout: post
title:  "在mingw上移植btstack"
date:   2017-08-15 19:06:05
categories: bluetooth
excerpt: 移植btstack
---

* content
{:toc}


## 目的

移植btstack到MSYS2环境

---

## 步骤

> * 1 安装libusb库libusb-1.0.21.tar.bz2
> * 2 安装winpty    
> * 3 修改\btstack-master\platform\posix\btstack_stdin_posix.c  第60行代码如下：

		if(kbhit())
		{
		    data = getch();
		    //read(stdin_source.fd, &data, 1);
		    if (stdin_handler){
		        (*stdin_handler)(data);
		    }
		}

> * 4 编译会缺少一个头文件：termios.h
> 可以把\usr\include\sys\termios.h拷贝到\btstack-master\port\libusb目录下
> * 5 在\port\libusb\btstack_config.h中注释掉 ENABLE_SCO_OVER_HCI
> * 6 在 \btstack-master\platform\posix\btstack_stdin_posix.c 中注释掉和 tcgetattr， tcsetattr 相关的代码。

