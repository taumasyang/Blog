---
title: “你帮我助”软件开发
date: 2022-09-22 11:23:20
updated: 2023-10-31 19:00:00
categories: 软件工程
tags: Python
---

[![GitHub Repo](https://img.shields.io/badge/GitHub-Help--Goods-blue)](https://github.com/tau-young/Help-Goods)

本项目是上海交通大学《CS3331-软件工程》课程作业。

# 题目描述

[![Canvas Page](https://img.shields.io/badge/Canvas-“你帮我助”软件开发-red)](https://oc.sjtu.edu.cn/courses/48894/assignments/181452)

> 个人项目（10月11日前完成）：
>
> 在疫情期间，各个小区居民发挥互助精神，进行物品交换，互通有无。请你编写一个物品交换软件
>
> 该程序允许添加物品的信息，删除物品的信息，显示物品列表，也允许查找物品的信息
>
> 你实现的程序可以采用命令行方式使用，但是鼓励提供GUI
>
> 程序实现完毕后，发布在Github上
>
> 进行PSP数据的统计，发布在你的技术博客上（内容请按照下表）
>
> ![表2-2 软件工程师的任务清单（中英对照）](https://oc.sjtu.edu.cn/courses/48894/files/5116866/preview?verifier=6ogAPjoGuA84rt4bfh1zJrdJRWq7HKCtwcwuH2B3)

<!--
# PSP 数据统计

|PSP 2.1|Timing|
|-|-|
|Planning||
|&emsp;Estimate|10min|
|Development||
|&emsp;Analysis|5min|
|&emsp;Design Spec|Not set|
|&emsp;Design Review||
|&emsp;Coding Standard|< 1min|
|&emsp;Design|30min|
|&emsp;Coding|2h|
|&emsp;Code Review||
|&emsp;Test|2h|
|Record Time Spent|Unrecorded|
|Test Report|No Report|
|Size Measurement|1 KB|
|Postmortem||
|Process Improvement Plan|More item details|
-->

# 功能介绍

使用 Python3 解释器执行 `goods.py` 文件，或直接运行 `dist/goods`（不在本仓库）。数据存储在 `goods.db` 中，删除此文件将导致数据丢失！输出结果需要 `prettytable` 包，如果使用解释器运行，请确保安装了它：
```sh
pip3 install prettytable
```

添加物品信息：
```sh
(goods) add <item1> <quantity1> <item2> <quantity2> ...
```
请在 `add` 命令后交替输入名称与数量，用空格分隔。如果数量为 1，则可省略。如：
```sh
add item 1
add item
```
都将添加一条数量为 1 的物品信息。如果物品已存在，则将更新物品数量。

删除物品信息：
```sh
(goods) del <item1> <quantity1> <item2> <quantity2> ...
```
用法与 `add` 命令一致。如果要删除的数量恰好等于库存数量，则该物品会被删除，否则将只更新数量。

列出所有物品信息：
```sh
(goods) list
```
查找物品信息：
```sh
(goods) search <item1> <item2> ...
```
支持模糊查找。

重置数据库：
```sh
(goods) reset
```