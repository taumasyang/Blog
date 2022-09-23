---
title: “你帮我助”软件开发
date: 2022-09-22 11:23:20
categories: 软件工程
tags: Python
---

本项目是上海交通大学《CS3331-软件工程》课程作业。

## 题目描述

[Canvas 页面（仅本课程参与人员有权限访问）](https://oc.sjtu.edu.cn/courses/48894/assignments/181452)

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

## 功能介绍

[GitHub 项目地址](https://github.com/tau-young/Help-Goods)

直接运行以进入交互式界面。

添加一条物品信息：
```sh
(goods) add item
```
删除一条物品信息：
```sh
(goods) del item
```
列出所有物品信息：
```sh
(goods) list
```
查找物品信息：
```sh
(goods) search item
```
重置数据库：
```sh
(goods) reset
```

## 具体实现

交互式终端使用 Python 提供的模块 `Cmd` 实现，数据库采用 `sqlite3`。