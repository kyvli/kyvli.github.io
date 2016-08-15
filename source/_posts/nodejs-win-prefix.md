---
title: Windows nodejs prefix变量设置
date: 2016-08-12 20:39:11
tags:
  - nodejs prefix
categories: 
  - windows
---



linux下，nodejs安装完成后，prefix变量值为nodejs安装目录，这样`npm install -g` 安装的包都会在安装目录下；

但是在win下，默认的prefix值并不是nodejs的安装目录，而是`C:\Users\username\AppData\Roaming`，然后`npm install -g`安装的包都会在`C:\Users\username\AppData\Roaming\npm`下，感觉还是放在安装目录比较好，也方便把配置好的整个nodejs环境部署到其他电脑；

方法很简单：

打开nodejs的安装目录下的这个文件，`D:\svr\nodejs\node_modules\npm\npmrc`，文件内容如下：

``` batch
prefix=${APPDATA}\npm
```  

注释这行即可，或者直接删掉这个文件

``` batch
; prefix=${APPDATA}\npm
```  

然后执行这个命令确认一下：

``` batch
npm config ls -l
``` 

可以看出来，其实npm的prefix默认值就是nodejs安装目录，只是这个配置文件覆盖了默认值。

还有个需要注意的地方就是，之前的nodejs版本如果设置prefix为nodejs安装目录，npm命令执行会报错，v4.4.7版本已经没有这个问题了。


