---
layout: post
title: homeassistant
slug: homeassistant
author: ymkNK
date: 2024-09-01 23:06:03 +0800
categories: [装修, 网络]
tags: [oneRiver,网络,软路由, mesh, NAS]
img: 1.jpg
---

## 参考

[智能家居 | HomeKit全屋智能家居的搭建 | 把家装进iPhone13 · 超长干货](https://www.bilibili.com/video/BV1XL41137kP/?spm_id_from=333.337.search-card.all.click&vd_source=31e016075d5dc418e05dd62618989320)

[智能家居2.0 | 米家天猫如何接入苹果HomeKit | Home Bridge | Home Assistant | 使用教程](https://www.bilibili.com/video/BV1pb4y1B7c8/?spm_id_from=333.999.0.0)


## HomeKit 

与苹果生态系统无缝集成，支持 Siri 语音控制

需要设备制造商提供 HomeKit 兼容性或通过 Homebridge 等工具实现兼容

Homebridge 不是一个独立的平台，而是一个工具，用于扩展 HomeKit 的设备兼容性，适合有一定技术基础的用户。

只支持兼容 HomeKit 的设备，设备种类较为有限

## Home Bridge 

Homebridge 是一个开源的 Node.js 服务器，旨在为不支持 HomeKit 的智能设备提供桥接，使其能够被 HomeKit 识别和控制。

扩展性强：通过插件支持大量不同品牌和类型的设备。

功能： 它充当中介，将各种非 HomeKit 设备“伪装”成 HomeKit 兼容设备，使得用户可以通过 Apple 设备来控制这些设备。常用于让原本不兼容 HomeKit 的设备（如一些智能灯、摄像头等）在 HomeKit 系统中运行。

## Home Assistant
Home Assistant 是一个开源的智能家居平台，旨在本地化管理和自动化控制各种智能设备，提供高度的自定义和扩展能

高度可定制：支持数千种设备和服务，通过 YAML 文件进行配置。

独立的开源智能家居平台


## home slicon 安装 assistant
[[教學] Home Assistant 的安裝方式 - 4. Apple Silicon Mac](https://forum.automata.id/t/topic/334)




### docker 听说有问题





## 场景

1. hey siri， 打开小米电视
2. 
