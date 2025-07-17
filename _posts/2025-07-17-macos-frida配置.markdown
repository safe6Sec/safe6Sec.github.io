---
layout: post
title:  "macos 多版本frida配置使用"
date:   2025-07-17 16:16:48 +0800
categories: Moblie sec
---

## 前言

一般不推荐用最新版的frida，这里说的最新版是指大版本。比如17，因为好多我觉得比较不错的工具都用不了，所以还是得用16，但是某些时候最新版也有他的用处。所以，多版本共存就显得很有必要。



## 笔记记录

以下内容，完全是，搭建过程中的记录，看看就行。



首先、依赖`virtualenv` 和 `virtualenvwrapper`

virtualenvwrapper是前者的功能增强工具。

用这个两个来快速的创建环境，因为frida，大版本直接差异比较大，需要多版本共存。

但是想装低版本frida，还得低版本的python，所以还要解决python版本共存的问题。

mac下、python版本共存问题，

特别是python3.8，其他版本都能命令行安装
直接下载安装包进行安装
https://www.python.org/downloads/release/python-3810/
安装后，环境变量就有python3.8，

为什么要3.8是因为,3.8支持frida低版本比如14，而这个版本可以有很多软件使用。
```
python3.8 -m pip install --upgrade pip
python3.8 -m pip install virtualenv virtualenvwrapper

```


windows 直接用这个是最方便，不怎么需要折腾
```
pip install virtualenvwrapper-win -i https://pypi.tuna.tsinghua.edu.cn/simple

mkvirtualenv 新建环境 
rmvirtualenv 删除环境

pip install frida-tools -i https://pypi.tuna.tsinghua.edu.cn/simple

切换环境
workon py310
```


mac 多版本python共存
首先，把python3.8设置为系统默认python版本，然后其他的由pyenv来管理
```
取消链接由brew安装的python，后面使用pyenv来做多版本管理，不能直接卸载，因为还有很多依赖的工具，比如sqlmap这些。
brew list |grep python
brew unlink python
brew unlink python@3.11

设置默认python
sudo rm -rf /usr/local/bin/python
sudo ln -s /usr/local/bin/python3 /usr/local/bin/python


第二步pyenv接管,python环境
brew reinstall pyenv

vim ~/.zshrc

export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv init -)"

source ~/.zshrc

安装一下其他版本的python

pyenv install 3.9
pyenv install 3.10
pyenv install 3.13

快速切换版本
pyenv global 3.11.9

换回3.8
pyenv global system

查看所有的版本
pyenv versions

查看当前版本
pyenv version

```

python版本共存解决之后，再来看frida版本问题
python3.8使用frida14,但是只支持Windows，mac彻底不支持frida14,最低版本为16

```
pyenv virtualenv system frida16
快速切换
pyenv activate frida16


pip list |grep frida  

frida           16.5.7

frida-tools      13.5.0


pip install frida-tools==13.5.0
pip install frida-tools==16.5.7

安装完成后，刷新一下环境，这里是个坑，一定要这样做。
pyenv deactivate frida16
pyenv activate frida16

adb forward tcp:27042 tcp:27042

frida-ps -Ua
```



## 关于frida版本

最新为17，这是server
https://github.com/frida/frida/releases

而本地使用需要cli工具，去链接frida server。使用python，需要安装frida-tools，注意这个cli工具的版本和frida版本不一样。不要混淆。

frida=tools最新版是14

自带的frida有很多特征，推荐一个去除特征版本
https://github.com/hackcatml/ajeossida



## frida 与 frida-tools 对应关系

来自看雪https://bbs.kanxue.com/thread-280436.htm

```
frida-tools==1.0.0 ------ 12.0.0<=frida<13.0.0  
frida-tools==1.1.0 ------ 12.0.0<=frida<13.0.0  
frida-tools==1.2.0 ------ 12.1.0<=frida<13.0.0  
frida-tools==1.2.1 ------ 12.1.0<=frida<13.0.0  
frida-tools==1.2.2 ------ 12.1.0<=frida<13.0.0  
frida-tools==1.2.3 ------ 12.1.0<=frida<13.0.0  
frida-tools==1.3.0 ------ 12.3.0<=frida<13.0.0  
frida-tools==1.3.1 ------ 12.3.0<=frida<13.0.0  
frida-tools==1.3.2 ------ 12.4.0<=frida<13.0.0  
frida-tools==2.0.0 ------ 12.5.3<=frida<13.0.0  
frida-tools==2.0.1 ------ 12.5.9<=frida<13.0.0  
frida-tools==2.0.2 ------ 12.5.9<=frida<13.0.0  
frida-tools==2.1.0 ------ 12.5.9<=frida<13.0.0  
frida-tools==2.1.1 ------ 12.5.9<=frida<13.0.0  
frida-tools==2.2.0 ------ 12.5.9<=frida<13.0.0  
frida-tools==3.0.0 ------ 12.6.17<=frida<13.0.0  
frida-tools==3.0.1 ------ 12.6.17<=frida<13.0.0  
frida-tools==4.0.0 ------ 12.6.21<=frida<13.0.0  
frida-tools==4.0.1 ------ 12.6.21<=frida<13.0.0  
frida-tools==4.0.2 ------ 12.6.21<=frida<13.0.0  
frida-tools==4.1.0 ------ 12.6.21<=frida<13.0.0  
frida-tools==5.0.0 ------ 12.6.21<=frida<13.0.0  
frida-tools==5.0.1 ------ 12.7.3<=frida<13.0.0  
frida-tools==5.1.0 ------ 12.7.3<=frida<13.0.0  
frida-tools==5.2.0 ------ 12.7.3<=frida<13.0.0  
frida-tools==5.3.0 ------ 12.7.3<=frida<13.0.0  
frida-tools==5.4.0 ------ 12.7.3<=frida<13.0.0  
frida-tools==6.0.0 ------ 12.8.5<=frida<13.0.0  
frida-tools==6.0.1 ------ 12.8.5<=frida<13.0.0  
frida-tools==7.0.0 ------ 12.8.12<=frida<13.0.0  
frida-tools==7.0.1 ------ 12.8.12<=frida<13.0.0  
frida-tools==7.0.2 ------ 12.8.12<=frida<13.0.0  
frida-tools==7.1.0 ------ 12.8.12<=frida<13.0.0  
frida-tools==7.2.0 ------ 12.8.12<=frida<13.0.0  
frida-tools==7.2.1 ------ 12.8.12<=frida<13.0.0  
frida-tools==7.2.2 ------ 12.8.12<=frida<13.0.0  
frida-tools==8.0.0 ------ 12.10.4<=frida<13.0.0  
frida-tools==8.0.1 ------ 12.10.4<=frida<13.0.0  
frida-tools==8.1.0 ------ 12.10.4<=frida<13.0.0  
frida-tools==8.1.1 ------ 12.10.4<=frida<13.0.0  
frida-tools==8.1.2 ------ 12.10.4<=frida<13.0.0  
frida-tools==8.1.3 ------ 12.10.4<=frida<13.0.0  
frida-tools==8.2.0 ------ 12.10.4<=frida<13.0.0  
frida-tools==9.0.0 ------ 14.0.0<=frida<15.0.0  
frida-tools==9.0.1 ------ 14.0.0<=frida<15.0.0  
frida-tools==9.1.0 ------ 14.2.0<=frida<15.0.0  
frida-tools==9.2.0 ------ 14.2.9<=frida<15.0.0  
frida-tools==9.2.1 ------ 14.2.9<=frida<15.0.0  
frida-tools==9.2.2 ------ 14.2.9<=frida<15.0.0  
frida-tools==9.2.3 ------ 14.2.9<=frida<15.0.0  
frida-tools==9.2.4 ------ 14.2.9<=frida<15.0.0  
frida-tools==9.2.5 ------ 14.2.9<=frida<15.0.0  
frida-tools==10.0.0 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.1.0 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.1.1 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.2.0 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.2.1 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.2.2 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.3.0 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.4.0 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.4.1 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.5.0 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.5.1 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.5.2 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.5.3 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.5.4 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.6.0 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.6.1 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.6.2 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.7.0 ------ 15.0.0<=frida<16.0.0  
frida-tools==10.8.0 ------ 15.0.0<=frida<16.0.0  
frida-tools==11.0.0 ------ 15.2.0<=frida<16.0.0  
frida-tools==12.0.0 ------ 16.0.0<=frida<17.0.0  
frida-tools==12.0.1 ------ 16.0.0<=frida<17.0.0  
frida-tools==12.0.2 ------ 16.0.0<=frida<17.0.0  
frida-tools==12.0.3 ------ 16.0.0<=frida<17.0.0  
frida-tools==12.0.4 ------ 16.0.0<=frida<17.0.0  
frida-tools==12.1.0 ------ 16.0.0<=frida<17.0.0  
frida-tools==12.1.1 ------ 16.0.9<=frida<17.0.0  
frida-tools==12.1.2 ------ 16.0.9<=frida<17.0.0  
frida-tools==12.1.3 ------ 16.0.9<=frida<17.0.0  
frida-tools==12.2.0 ------ 16.0.9<=frida<17.0.0  
frida-tools==12.2.1 ------ 16.0.9<=frida<17.0.0  
frida-tools==12.3.0 ------ 16.0.9<=frida<17.0.0
```



## 一些frida的常用命令

随手记

环境切换
```
pyenv activate frida16

```

查看当前手机运行的进程

```

frida-ps -U  
```

快速查看，当前active和包名

```
adb shell dumpsys window | grep CurrentFocus

```


指定端口启动
```
frida server 默认端口：27042 
taimen:/ $ su 
taimen:/ # cd data/local/tmp/ 
taimen:/data/local/tmp # ./fs1280 -l 0.0.0.0:6666
```

端口转发
```
adb forward tcp:27042 tcp:27042
```

查看已链接的设备
```
frida-ls-devices

Id                 Type    Name             OS
-----------------  ------  ---------------  ----------
local              local   Local System     macOS 15.5
192.168.2.78:5555  usb     KB2000
barebone           remote  GDB Remote Stub
socket             remote  Local Socket

```


Spawn模式加载
```nginx
frida -U -f 包名 -l hook.js
```

attach模式 ：

```nginx
frida -U 进程名 -l hook.js
```

使用
```
frida -U GM -l r0tracer.js

```

## objection使用

其实这才是这篇文章的目录，就是为了用这个。所以一定要解决多版本共存的问题。



最后折腾完后，发现frida16就能使用objection。下面贴一下常用命令，很多来自吾爱破解，正己大佬写的，不了解这个工具的，可以去看视频学习一下。



使用之前，先切换到可用的环境
```
pyenv activate frida16
```


```
objection --help(help命令)
Checking for a newer version of objection...
Usage: objection [OPTIONS] COMMAND [ARGS]...

       _   _         _   _
   ___| |_|_|___ ___| |_|_|___ ___
  | . | . | | -_|  _|  _| | . |   |
  |___|___| |___|___|_| |_|___|_|_|
        |___|(object)inject(ion)

       Runtime Mobile Exploration
          by: @leonjza from @sensepost

  默认情况下，通信将通过USB进行，除非提供了`--network`选项。

选项:
  -N, --network            使用网络连接而不是USB连接。
  -h, --host TEXT          [默认: 127.0.0.1]
  -p, --port INTEGER       [默认: 27042]
  -ah, --api-host TEXT     [默认: 127.0.0.1]
  -ap, --api-port INTEGER  [默认: 8888]
  -g, --gadget TEXT        要连接的Frida Gadget/进程的名称。 [默认: Gadget]
  -S, --serial TEXT        要连接的设备序列号。
  -d, --debug              启用带有详细输出的调试模式。(在堆栈跟踪中包括代{过}{滤}理源图)
  --help                   显示此消息并退出。

命令:
  api          以无头模式启动objection API服务器。
  device-type  获取关于已连接设备的信息。
  explore      启动objection探索REPL。
  patchapk     使用frida-gadget.so补丁一个APK。
  patchipa     使用FridaGadget dylib补丁一个IPA。
  run          运行单个objection命令。
  signapk      使用objection密钥对APK进行Zipalign和签名。
  version      打印当前版本并退出。
```




获取包名
```
adb shell dumpsys window | grep CurrentFocus

frida-ps -Ua
```

```
objection -g 包名或进程名 explore
```

启动前hook
```
objection -g 进程名 explore --startup-command "android hooking watch class 路径.类名"
```

加载插件
```
objection -g GM explore -P /Users/safe6sec/tools/apk/objection-plugins
```

插件使用
```
 plugin dexdump

```


```
memory list modules   -查看内存中加载的库

memory list exports so名称 - 查看库的导出函数

android hooking list activities -查看内存中加载的activity

android hooking list services -查看内存中加载的services



android intent launch_activity 类名  启动activity


 android sslpinning disable    关闭ssl校验

 android root disable        关闭root检测


hook 该类所有方法

android hooking watch class 类名

hook特定方法
android hooking watch class_method 类名.方法名 --dump-args --dump-return --dump-backtrace

android hooking watch class com.j75ed30089.y2295a1512.MainActivity --dump-args --dump-backtrace --dump-return



hook构造方法
android hooking watch class_method 类名.$init

android hooking watch class_method 类名.方法名


memory search --string --offsets-only //搜索内存


android heap search instances 类名(命令) 搜索内存实例，可以用来调用


android heap execute <handle> getPublicInt(实例的hashcode+方法名) 
如果是带参数的方法，则需要进入编辑器环境   
android heap evaluate <handle>   
console.log(clazz.a("吾爱破解")); 
按住esc+enter触发



android hooking list classes -列出内存中所有的类(结果比静态分析的更准确)


android hooking search classes 关键类名 -在内存中所有已加载的类中搜索包含特定关键词的类

android hooking search methods 关键方法名 -在内存中所有已加载的类的方法中搜索包含特定关键词的方法(一般不建议使用，特别耗时，还可能崩溃)

android hooking list class_methods 类名 -内存漫游类中的所有方法


```

插件
```
# 搜索类
plugin wallbreaker objectsearch LoginActivity
//返回：
com.example.androiddemo.Activity.LoginActivity
com.example.androiddemo.Activity.LoginActivity$1
 
# 根据类名搜索内存中已经被创建的实例，列出 handle 和 toString() 的结果 --fullname 打印完整的包名
plugin wallbreaker classdump com.example.androiddemo.Activity.LoginActivity --fullname
 
# 搜索对象
plugin wallbreaker objectsearch com.example.androiddemo.Activity.LoginActivity
//返回：
[0x2262]: com.example.androiddemo.Activity.LoginActivity@d8a5160
 
# 查看对象的一些属性和方法
plugin wallbreaker objectdump 0x2262 --fullname

查看某个activit
plugin wallbreaker classdump com.j75ed.MainActivity --fullname


```


dump dex
```
plugin dexdump search com.j75ed.MainActivity

plugin dexdump dump

```
