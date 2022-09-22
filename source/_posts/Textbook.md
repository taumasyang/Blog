---
title: 获取、加工、使用电子课本
date: 2022-09-22 17:42:26
categories: 知识分享
tags:
---

## 获取电子课本

### Z-Library

> [Z-Library](https://1lib.domains/) 是世界上最大的在线图书馆之一，它拥有超过 11,179,413 的书籍和 84,837,000 的文章。我们的目标是让每个人都能获得文学作品。

Z-Library 有很多镜像站，对于每个镜像站来说，可访问性使不确定的，因此只记住某个站点的地址可能会让你不久以后就无法访问。官方做了一个链接，每次自动寻找可用的站点，收藏好[这个地址](https://1lib.domains/)就可以了。

对于游客来说，Z-Library 限制了每个 IP 地址每天可下载的数量（5 本）。但是，对于网络运营商来说，他们很可能会使众多用户用用一个公网 IP 地址对外访问，因此，你很可能发现明明自己没有下载过书就被限制了。最好的方法是注册一个 Z-Library 账号，它不仅能避免其他使用同一个公网 IP 的用户侵占你的配额，还能允许你每天下载最多 10 本电子书。一般来说，一个学期的课程不会用到超过 10 本参考书，因此在一天内找齐你要的所有书是没有问题的。

### Library Genesis

> 图书馆创世纪聚合器是一个旨在收集和编目项目描述的社区，大部分是科学、科学和技术方向，以及文件元数据。除了描述之外，聚合器只包含由用户主持的第三方资源的链接。网站上发布的所有信息都是从公开的公共互联网资源中收集的，仅用于提供信息的目的。

Library Genesis 是一个俄罗斯的找书网站。它也有很多站点，在以下链接中寻找可访问的即可。

[libgen.io](https://libgen.io), [libgen.org](https://libgen.org), [libgen.li](https://libgen.li), [libgen.gs](https://libgen.gs), [libgen.lc](https://libgen.lc)

## 加工电子课本

从上述网站下载的电子课本大多是影印版，需要对它们进行**光学文字识别**（OCR）和制作目录。

### 光学文字识别

我曾尝试过许多 PDF OCR 解决方案，大多数在识别中文是都会在每两个汉字中间增加一些无意义的空格。目前我发现的最好的文字识别软件是 [ABBYY FineReader PDF](https://pdf.abbyy.com/finereader-pdf-for-mac/)。需注意，这是收费软件，但是我们可以找到[破解版](https://macwk.com/soft/ABBYY-FineReader-PDF)。

安装软件后立即打开，输入激活码激活软件，然后设置识别语言为「简体中文和英文，**简单数学公式**」。对于计算机类教材，还可以添加对应的程序设计语言。

回到我们刚才下载的电子课本，选择使用 ABBYY FineReader PDF 打开，软件会自动添加 PDF 页面并开始识别。识别过程是多核的，占用资源比较多，性能不太够的电脑可以考虑暂停其他操作。识别完后，对照警告检查页面后，选择导出。导出时可按需选择「使用 MRC 压缩图像」，打开能显著缩小导出的文件，但对于一些数学公式可能会渲染异常。如果发生这样的情况，就不要勾选。确认导出的文件没有问题后，退出 ABBYY FineReader PDF。

### 制作目录

我们采用 [Coherent PDF Tools](https://www.coherentpdf.com/) 作为后续处理 PDF 的工具。对照官网的介绍下载、安装 `cpdf` 命令行工具。

我们先检查电子课本中是否自带目录。

```sh
cpdf -list-bookmarks -utf8 textbook.pdf
```

{% note info %}
如果不加 `-utf8` 开关，`cpdf` 则只会输出目录中的 ASCII 字符。
{% endnote %}

如果有输出，表示电子课本自带目录，直接使用即可。

```sh
cpdf -list-bookmarks -utf8 textbook.pdf > bookmarks.txt
```

{% note warning %}
ABBYY FineReader PDF 在导出时会重新制作 PDF，因此会移除原始 PDF 的书签。无论何时，对于 ABBYY FineReader PDF 导出的文件都需要重新添加目录。
{% endnote %}

如果原始电子课本没有自带目录，就需要我们手动制作。目录文件的格式参考[官方文档](https://www.coherentpdf.com/cpdfmanual.pdf)，制作过程在此不再赘述。

制作好我们的目录文件 `bookmarks.txt` 后，需要将其添加至电子课本中。

```sh
cpdf -utf8 -add-bookmarks bookmarks.txt textbook-1.pdf -o textbook-final.pdf
```

{% note warning %}
注意，这条命令的 `-utf8` 的位置和上面一条的不一样。
{% endnote %}

检查输出的 PDF 文件，确认无误后可以删除先前产生的中间文件。

## 使用电子课本

将我们加工完成的电子课本文件导入 [MarginNote 3](https://apps.apple.com/cn/app/marginnote-3/id1348317163) 即可在所有设备上阅读、学习。