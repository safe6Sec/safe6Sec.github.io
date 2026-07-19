---
layout: post
title:  "解决mac版FinalShell内存异常问题"
date:   2026-03-01 01:16:48 +0800
categories: network
---

## 前言
一直被FinalShell的内存问题困扰，开了几天占用十多个G，最后让ai排查，是由于**Rosetta 转译 导致缓存爆炸**问题。

主要是mac上打包的版本，是用的Intel 下的jre。而Intel 版在 ARM Mac 上运行，需要通过 Rosetta 转译才能跑起来。

## 解决方案

可以写一个bash脚本，使用系统已有的arm版jdk(自己提前安装)，来直接启动jar。

1、替换启动程序

准备一个新的bash启动脚本。替换 `/Applications/FinalShell.app/Contents/MacOS/FinalShell`文件


```bash

#!/bin/bash
  APP_DIR="$(cd "$(dirname "$0")/../app" && pwd)"
  JAVA_HOME="/Library/Java/JavaVirtualMachines/jdk-21.jdk/Contents/Home"

  exec "$JAVA_HOME/bin/java" \
      -Xdock:icon="/Applications/FinalShell.app/Contents/Resources/FinalShell.
      icns" \
      -Xmx512m \
      -Xms64m \
      -XX:MinHeapFreeRatio=20 \
      -XX:MaxHeapFreeRatio=40 \
      -Dsun.java2d.opengl=true \
      --add-exports=java.desktop/com.apple.eawt=ALL-UNNAMED \
      -Duser.dir="$APP_DIR" \
      -Dapple.awt.application.name=FinalShell \
      -cp "$APP_DIR/finalshell.jar:$APP_DIR/lib-run/bcprov.jar" \
      st

```


2、步骤

```
备份，原版，然后用bash脚本替换这个
sudo mv /Applications/FinalShell.app/Contents/MacOS/FinalShell \
/Applications/FinalShell.app/Contents/MacOS/FinalShell.orig


最后加权限即可。
sudo chmod 755 /Applications/FinalShell.app/Contents/MacOS/FinalShell

```

改完之后，速度也快了很多。
