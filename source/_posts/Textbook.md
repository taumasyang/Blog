---
title: 获取、加工和使用电子课本
date: 2022-09-22 17:42:26
categories: 技术分享
---

纸质版的课本通常不便宜，尤其是国外出版的教材，而且它们携带起来非常重且不方便。如果有一台 iPad 或者类似的平板电脑，那么使用电子课本将会变得非常方便。

## 获取电子课本

### Z-Library

{% note danger %}
（2022 年 11 月 4 日更新）Z-Library 网站据传被美国警方查封。[全世界最大的电子图书馆 z-lib 被美国警方查封，所有域名无法访问 - 水源广场 / 热点新闻 - 水源社区](https://shuiyuan.sjtu.edu.cn/t/topic/107743)
{% endnote %}

{% note warning %}
（2022 年 11 月 11 日更新）Z-Library 已恢复服务，但只能通过 Tor 网络访问。

> Z-library website is currently available only in Tor network. You can find out more and download [Tor browser](https://www.torproject.org/)
>
> Tor site: [bookszlibb74ugqojhzhg2a63w5i2atv5bqarulgczawnbmsb6s6qead.onion](http://bookszlibb74ugqojhzhg2a63w5i2atv5bqarulgczawnbmsb6s6qead.onion/)

可以使用 Homebrew 安装 Tor browser（需要科学上网）：
```sh
brew install tor-browser
```
{% endnote %}

{% note info %}
（2023 年 2 月 11 日更新）Z-Library 已恢复常规网络访问。根据官方博客，用户只需要在 [singlelogin.me](https://singlelogin.me) 登录自己的账户，就将被重定向到个人专属域名。或者可以直接访问个人专属域名并验证密码。访问个人专属域名仍需要科学上网。请注意不要以任何形式泄露个人专属域名！

为了防止泄露我的专属域名，我将原文 {% post_link Z-Library-Back-to-Clearnet %} 转载到了本站，如有需要可以根据官方指引进行操作。
{% endnote %}

> [Z-Library](https://1lib.domains/) 是世界上最大的在线图书馆之一，它拥有超过 11,179,413 的书籍和 84,837,000 的文章。我们的目标是让每个人都能获得文学作品。

Z-Library 有很多镜像站，对于每个镜像站来说，可访问性是不确定的，因此只记住某个站点的地址可能会让你不久以后就无法访问。官方做了一个链接，每次自动寻找可用的站点，收藏好[这个地址](https://1lib.domains/)就可以了。

对于游客来说，Z-Library 限制了每个 IP 地址每天可下载的数量（5 本）。但是，对于网络运营商来说，他们很可能会使众多用户用用一个公网 IP 地址对外访问，因此，你很可能发现明明自己没有下载过书就被限制了。最好的方法是注册一个 Z-Library 账号，它不仅能避免其他使用同一个公网 IP 的用户侵占你的配额，还能允许你每天下载最多 10 本电子书。一般来说，一个学期的课程不会用到超过 10 本参考书，因此在一天内找齐你要的所有书是没有问题的。

### Library Genesis

> 图书馆创世纪是一个旨在收集和编目项目描述的社区，大部分是科学、科学和技术方向，以及文件元数据。除了描述之外，聚合器只包含由用户主持的第三方资源的链接。网站上发布的所有信息都是从公开的公共互联网资源中收集的，仅用于提供信息的目的。

Library Genesis 是一个俄罗斯的找书网站。它也有很多站点，在以下链接中寻找可访问的即可。科学上网可以稳定访问下列任意站点。

[libgen.io](https://libgen.io), [libgen.org](https://libgen.org), [libgen.li](https://libgen.li), [libgen.gs](https://libgen.gs), [libgen.lc](https://libgen.lc)

## 加工电子课本

网上获取的电子课本大多没有文字图层，为方便今后使用的时候快速定位和查找内容，需要提前对它们进行**光学文字识别**（OCR）和制作目录。

### 光学文字识别

我曾尝试过许多 PDF OCR 解决方案，大多数在识别中文时都会在每两个汉字中间增加一些无意义的空格。目前我发现的最好的文字识别软件是 [ABBYY FineReader PDF](https://pdf.abbyy.com/finereader-pdf-for-mac/)。需注意，这是收费软件，但是我们可以找到[破解版](https://macapp.org.cn/app/abbyy-finereader-pdf.html)。

安装软件后立即打开，输入激活码激活软件，然后设置识别语言为「简体中文和英文，**简单数学公式**」。对于计算机类教材，还可以添加对应的程序设计语言。

回到我们刚才下载的电子课本，选择打开方式为 ABBYY FineReader PDF，软件会自动添加 PDF 页面并开始识别。识别过程是多核的，占用资源比较多，性能不太够的电脑可以考虑暂停其他操作。识别完后，对照警告检查页面后，选择导出。导出时可按需选择「使用 MRC 压缩图像」，打开该选项能显著缩小导出的文件，但部分数学公式可能会渲染异常。如果发生这样的情况，就不要勾选。导出的过程是单核的，因此可能会非常慢。确认导出的文件没有问题后，就可以退出 ABBYY FineReader PDF 了。

{% note warning %}
在识别下一本书之前，请先退出 ABBYY FineReader PDF，否则它将会将下一本书的页面添加到上一本书的末尾。
{% endnote %}
{% note danger %}
ABBYY FineReader PDF 无法处理使用 `jbig2` 编码的 PDF 文件。这种文件是纯黑白的，并且十分少见。解决方案：将 PDF 文件每页都导出为图像文件再进行处理。
{% endnote %}

### 制作目录

我们采用 [Coherent PDF Tools](https://www.coherentpdf.com/) 作为后续处理 PDF 的工具。对照官网的介绍下载、安装 `cpdf` 命令行工具。一般建议将下载好的对应版本的命令行工具拷贝到 `/usr/local/` 目录下。

我们先检查电子课本中是否自带目录：
```sh
cpdf -list-bookmarks -utf8 textbook.pdf
```
{% note warning %}
如果不加 `-utf8` 开关，`cpdf` 则只会输出目录中的 ASCII 字符，如果有汉字或者其他非 ASCII 字符，它们将会被忽略。
{% endnote %}
如果有输出，表示电子课本自带目录，导出后稍作处理就可以作为新目录插入到经过 OCR 的文件中。
```sh
cpdf -list-bookmarks -utf8 textbook.pdf > bookmarks.txt
```
{% note warning %}
ABBYY FineReader PDF 在导出时会重新制作 PDF，因此不会保留原始 PDF 的书签。无论何时，对于 ABBYY FineReader PDF 导出的文件都需要重新添加目录。
{% endnote %}
如果原始电子课本没有自带目录，就需要我们手动制作。目录文件的格式参考[官方文档](https://www.coherentpdf.com/cpdfmanual.pdf)，制作过程在此不再赘述。

制作好我们的目录文件 `bookmarks.txt` 后，需要将其添加至 PDF 文件中。
```sh
cpdf -utf8 -add-bookmarks bookmarks.txt textbook-ocr.pdf -o textbook-final.pdf
```
{% note warning %}
这条命令的 `-utf8` 的位置和上面一条的不一样。
{% endnote %}
检查输出的 PDF 文件，确认无误后可以删除先前产生的中间文件。

### 添加逻辑页码

一般来说，书籍的正文部分会重新编号页码，这就导致了我们阅览 PDF 文件的正文部分会出现书中的页码与文件的页码不一致的问题。为了使两者同步，我们可以往 PDF 文件中添加**逻辑页码**。在 Coherent PDF Tools 中，这样的操作被称为添加「页面标签」（Page Labels）。我们可以为页面标签指定不同的页面范围、样式、前缀、起始编号等等。默认的样式是十进制数字，默认的起始编号是 1。

> |样式|预览|
> |-:|:-|
> |`DecimalArabic`|1, 2, 3, 4, 5...|
> |`LowercaseRoman`|i, ii, iii, iv, v...|
> |`UppercaseRoman`|I, II, III, IV, V...|
> |`LowercaseLetters`|a, b, c, ..., z, aa, bb...|
> |`UppercaseLetters`|A, B, C, ..., Z, AA, BB...|
> |`NoLabelPrefixOnly`|No number, but a prefix will be used if defined.|
> 
> 考虑一份有 20 页的 PDF 文件，我们要向其添加如下的页面标签：
> 
> i, ii, iii, iv, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, A-0, A-1, A-2, A-3, A-4, A-5
> 
> 我们可以这么操作：
> ```sh
> cpdf -add-page-labels in.pdf 1-4 -label-style LowercaseRoman -o out.pdf
> cpdf -add-page-labels out.pdf 5-14 -o out.pdf
> cpdf -add-page-labels out.pdf 15-20 -label-prefix "A-" -label-startval 0 -o out.pdf
> ```

## 使用电子课本

将我们加工完成的电子课本文件导入 [MarginNote 3](https://apps.apple.com/cn/app/marginnote-3/id1348317163) 或者其他文献阅读器中即可在所有设备上阅读、学习。经过我们对课本文件的处理，现在可以通过目录或页码快速、准确地访问某一特定内容。

如果嫌弃 MarginNote 3 在 Mac 上性能低下，可以考虑使用 VSCode 和 PDF 插件。如果安装了 LaTeX Workshop，由于该插件自带 `PDF.js` 用于预览生成的文件，无需另外安装插件即可查看 PDF 文件。