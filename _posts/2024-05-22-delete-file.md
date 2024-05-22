---
layout: post
title: delete-file
slug: delete-file
author: ymkNK
date: 2024-05-22 11:06:17 +0800
categories: [开发, 操作系统]
tags: 
img: 1.jpg
---


## 删除前9000个文件

```shell
ls -1 | head -n 9000 | xargs rm
```
