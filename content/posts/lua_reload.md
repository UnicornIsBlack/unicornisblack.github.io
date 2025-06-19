---
title: "游戏运行时reload lua脚本修改"
date: 2025-06-12T11:08:05+08:00
draft: false
image: brand_image.jpg
tags: ["lua","xlua", "unity"]
series: "Unity with xlua"
---


## 背景

&emsp;&emsp;Unity with xlua框架搭建过程中，需要一个reload脚本在游戏运行时更新lua脚本的变化，在腾讯UE5 Lyra开源项目中找到了一个reload脚本(该脚本参考于云风大神)，经过简单修改，适配了Unity。该脚本中使用沙盒的形式，hook原先的require等函数，根据最后修改时间，来更新已加载模块的变更。

&emsp;&emsp;原生Lua对文件系统的操作较少，如需获取文件修改时间等，很困难。Github上有luasystemfile开源项目，支持了更多文件系统的操作，遂将其集成到了xlua中，实际使用时发现在require("lfs")时，Unity会崩溃，开始以为是集成出现了问题，忽然想到之前在require三方库时，也出现过崩溃，需要在require reload脚本前加载三方库，注释加载reload脚本代码后，lfs库调用正常。

&emsp;&emsp;既然找到了问题方向，就要找到根本原因，在一番打印、调试后放弃，猜测是因为reload脚本hook了require函数，导致加载三方库时出现了问题，使用原require函数就没有问题，具体原因可能得等后续更加熟悉Unity、Lua和xlua后再定位。在未了解真实原因的情况下，还需继续往下走，需要对reload函数进行修改，放弃hook require函数以及沙盒的方式。

## 实现

&emsp;&emsp;原先的reload脚本是通过hook require函数，在require模块的时候（这部分需要再打日志来确定一下）

~~&emsp;&emsp;在加载reload时，记录当前系统时间，全量reload时，对比已加载模块最后修改时间，将修改时间在启动时间后的模块重新加载~~

&emsp;&emsp;经过后续测试，在hook require函数那里过滤三方库引用，直接使用原生require就没有问题了。
