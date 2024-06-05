---
layout: post
title: Goroutine池子
slug: goroutine池子
author: ymkNK
date: 2024-05-25 19:11:41 +0800
categories: [开发, golang]
tags: [goroutine， golang]
img: 1.jpg
---


[Go 每日一库之 ants](https://darjun.github.io/2021/06/03/godailylib/ants/)


![并发/并行](https://miro.medium.com/v2/resize:fit:409/1*_4B2PKsJn9pUz3jbTnBnYw.png)


[golang教程](https://www.topgoer.com/)


## concurrent map read and map write

[Golang fatal error: concurrent map read and map write](https://stackoverflow.com/questions/45585589/golang-fatal-error-concurrent-map-read-and-map-write)


## go run -race server.go

`go run -race server.go` 是用来运行 Go 程序并启用数据竞争检测的。
数据竞争（data race）是指多个 goroutine 同时访问相同的内存位置，并且至少有一个 goroutine 执行写操作，而这些操作没有进行适当的同步。
当你的程序存在数据竞争时，可能会导致难以预测的行为和错误。




> go run -race server.go
{: .prompt-danger }

