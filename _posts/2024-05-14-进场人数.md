---
title: 工地进场人数统计
date: 2024-05-14 11:19:45 +0800
author: john
categories: [code, python]
tags: oneRiver excel
layout: post
---


```python
import openpyxl
from openpyxl.chart import LineChart, Reference

# 创建一个新的Excel工作簿
wb = openpyxl.Workbook()
ws = wb.active
ws.title = "进场人数"

# 数据
data = [
    ("日期", "人数"),
    ("1号", 26),
    ("2号", 37),
    ("3号", 53),
    ("4号", 61),
    ("5号", 43),
    ("6号", 56),
    ("7号", 62)
]

# 将数据写入工作表
for row in data:
    ws.append(row)

# 创建折线图
chart = LineChart()
chart.title = "学校进场人数变化图"
chart.x_axis.title = "日期"
chart.y_axis.title = "人数"

# 设置图表数据
data = Reference(ws, min_col=2, min_row=1, max_col=2, max_row=len(data))
categories = Reference(ws, min_col=1, min_row=2, max_row=len(data))
chart.add_data(data, titles_from_data=True)
chart.set_categories(categories)

# 将图表添加到工作表
ws.add_chart(chart, "D2")

# 保存Excel文件
wb.save("进场人数变化图.xlsx")
```
