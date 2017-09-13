---
layout: post
title:  "stm32f4-discovery开发板运行elua"
date:   2017-09-12 19:06:05
categories: lua
excerpt: elua on stm32f4
---

* content
{:toc}


## 目的

在stm32f4discovery开发板上运行elua,PC环境为win7

---

## 步骤

> * 1   准备环境为Msys2+mingw 

> * 2    在win7上安装[lua for windows](https://code.google.com/p/luaforwindows/) 

> * 3   在MSYS2中配置.bashrc,把lua.exe加入PATH变量中：

		PATH=$PATH:"/C/Program Files (x86)/Lua/5.1/"
		
>    另外由于MSYS2操作windows的一些程序是，标准的输入输出有问题，所以用winpty启动lua,.bashrc中加入:

		alias lua='winpty lua.exe'	

		
> * 4  下载[ARM编译器](https://launchpad.net/gcc-arm-embedded/+download),安装后在.bashrc中加入编译器路径

		PATH=$PATH:"/mingw32/tools/GNU Tools ARM Embedded/5.4 2016q3/bin"
		
> * 5  下载[elua源代码](https://github.com/elua/elua)

> * 6  在MSYS2中进入elua源代码所在目录输入：

		lua build_elua.lua board=stm32f4discovery  prog
		
>    如果运行正常会生成elua_lua_stm32f4discovery.hex文件

> * 7 安装STM32相关的软件：
>      *		STM32 ST-LINK Utility (主要用安装ST-LINK驱动)
>      *		ST Visual Programmer (主要用来下载hex文件到硬件板子)
>      *		STM32 USB转串口驱动 Virtual COM Port Driver(V1.3.1) (为了让PC识别STM32 USB转串口)

> * 8 用miniUSB口下载hex文件，然后重新上电

> * 9 连接microUSB口到PC,这时如果正常PC会看到2个COM口，一个是ST-LINK的(miniUSB)一个是STM32的(microUSB)

> * 10 安装[ExtraPuTTY软件](https://sourceforge.net/projects/extraputty/)  选择microUSB对应的串口，打开。

> * 11 这时就可以通过命令来控制stm32discovery板子了

> * 12 如果要通过PC传递一个LUA文件运行，可以在ExtraPutty中输入recv,然后选择File Transfer->Xmodem->Send选择lua文件

> * 13 如果要把lua文件固化到板子上，可以吧lua文件放在elua源代码中romfs目录下重新编译，然后下载hex文件到板子

> * 14 一个lua控制led闪烁的例子：

		ledpin = pio.PD_13
		pio.pin.setdir(pio.OUTPUT, ledpin)
		for i=1,10,1 do
		tmr.delay(1,1000000)
		pio.pin.sethigh(ledpin)
		tmr.delay(1,1000000)
		pio.pin.setlow(ledpin)
		end
		



