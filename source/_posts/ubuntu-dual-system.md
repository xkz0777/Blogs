---
title: Ubuntu 双系统
date: 2022-02-02 15:58:20
tags: Linux
categories: Linux
author: xkz
img: /medias/featureimages/dual-system.jpg
summary: 在 Windows 上装 Ubuntu 双系统
---

# 记录装 Ubuntu 双系统的过程

机型为联想 Yoga14s amd 版, 大体按照 https://www.cnblogs.com/masbay/p/10745170.html 这篇文章进行操作(情况为 UEFI 加单硬盘), 这里主要记录不同之处.

首先是 BIOS, 狂按 F2 进入以后并不能直接选择 USB 启动, 而是需要先到 Securty 中 disable Secure Boot, 然后保存再次重启, 狂按 F12, 选择 USB HDD 开始安装系统.

在手动分区时, 分区类型全部选择逻辑分区即可.

第一次分区完准备安装以后卡死过一次, 重启然后重复操作发现没有影响, 装机之前也没有备份, 感觉装双系统装崩的可能性还是比较小的.