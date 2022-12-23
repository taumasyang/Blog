---
title: 「你帮我助」软件开发
date: 2022-12-23 23:45:02
categories: 软件工程
tags: 
  - Python
  - Django
---

本项目是上海交通大学 CS-3331 软件工程课程大作业，同时也是 [Help-Goods](https://github.com/tau-young/Help-Goods) 项目的 GUI 版本，但是经过了完全的重构。

原项目采用 CLI 形式，由 Python Script 写成，理论上只要安装了 Python 3.y 解释器的机器就能运行。本项目使用 Django 制作成网页应用，因此在任何浏览器上都能访问。然而，运行本项目的服务器仍需要安装 Python 3.y 并正确部署。

## 作业描述

[原始页面](https://oc.sjtu.edu.cn/courses/48894/assignments/193007)（仅限该课程参与人员有权访问）

> ### “你帮我助”软件开发（Final)
>
> #### 新的功能需求：
>
> 1. 物品有公共的信息（物品名称，物品说明，物品所在地址，联系人手机，邮箱）。为了便于管理和查询，物品可以分成不同的类别（例如食品、书籍、工具等），不同类别的物品可能有不同的属性（例如食品有保质期，数量；书籍有作者，出版社等）。
> 2. 互帮互助系统有两种类型的用户：管理员和普通用户。
> - 管理员可以设置新的物品类型（定义物品类型的名称和各个属性），修改物品类型。
> - 普通用户在添加物品时先选择物品类型，然后再填入物品信息。普通用户搜寻物品时，需要先选择类型，再输入关键字，关键字可以再用户名称和说明中进行匹配。
> 普通用户需要注册（填入基本信息，包括住址，联系方式等），管理员批准后才能成为正式用户。
>
> 3. 为了便于使用上述功能，软件需要提供 GUI。
>
> #### 文档需求：
>
> 1. 需要提供一个文档：其中包括（1）用例模型；（2）针对用例画顺序图；（3）类图。
>
> #### 作业完成要求：
>
> 作业需要在 16 周前提交，并发布在你的 GitHub 仓库中；
> 作业完成后需要进行演示，请提前一周与助教约好。（每周周二下午都可以约）；演示的内容包括写的文档、软件的功能（包括第一次，第二次功能）。
> 在技术博客上写一篇总结文章，对照软件工程的知识，对开发该程序的体会进行总结。

## 使用说明

本仓库不包含任何测试用数据以及项目开发缓存，所有数据需要自己生成。

安装 Django
```sh
pip install django
```
克隆本仓库
```sh
git clone https://github.com/tau-young/HelpGoodsWeb.git
```
切换到根目录
```sh
cd HelpGoodsWeb
```
注册迁移项
```sh
python manage.py makemigrations
python manage.py migrate
```
创建超级用户（如果需要访问后台管理页面）
```sh
python manage.py createsuperuser
```
启动服务器
```sh
python manage.py runserver
```
在浏览器中访问 <localhost:8000/user>。

## 用例图

![Use Case](usecase.svg)

## 顺序图

![Sequence Register](sequence-register.svg)
![Sequence Login](sequence-login.svg)
![Sequence Logout](sequence-logout.svg)
![Sequence View User Info](sequence-viewuserinfo.svg)
![Sequence View Item List](sequence-viewitemlist.svg)
![Sequence View Item Info](sequence-viewiteminfo.svg)
![Sequence Post New Item](sequence-postnewitem.svg)
![Sequence Modify Item](sequence-modifyitem.svg)
![Sequence Grant User](sequence-grantuser.svg)
![Sequence Set New Item Type](sequence-setnewitemtype.svg)

## 类图

![Class Graph](class.svg)