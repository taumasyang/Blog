---
title: 从 Dewey 上批量下载数据
date: 2023-04-01 15:42:10
categories: 技术分享
tags: Shell
---

[Dewey | Academic Research Data](https://www.deweydata.io/) 明明是个数据批发商，却不提供批量下载的接口，真是让人摸不着头脑。

![](No-Head.png)

天下苦 Dewey 久矣！于是，动手能力强的我们自然不会傻傻的在浏览器中一个一个地点击下载。通过运行脚本，我们可以一次性把我们需要的数据都下载下来。

# Dewey 存储数据的规律

登录 Dewey，在个人账户下选择「Browse My Files」，就能看到自己的数据文件了。Dewey 按日期整理数据，根目录下的路径依次为 `/YEAR/MONTH/DATE/SOURCE/DATA`。

![Dewey 根目录](Dewey-Root.png)

以 2020 年 1 月的 SafeGraph 数据为例，我们先找到对应文件夹下的文件，查看详细信息。

![Dewey 文件信息](Dewey-File-Information.png)

我们注意到其中有一条是 `url` 信息，这就是这个文件在服务器中的地址了。结合网站域名，我们就能分析出这一个文件的下载地址是 https://marketplace.deweydata.io/api/data/v2/data/2020/01/01/SAFEGRAPH/MP/20200101-safegraph_mp_cpgp_part1_0 。

类似的文件在这个目录下共有 29 个 part，不难发现它们之间的区别就在于 part 后面的数字。

# cURL 下载工具

cURL 是基于网络协议，对指定URL进行网络传输的工具。若 URL 的终点是一个文件，那么也可以进行文件传输。

![cURL Logo](Curl-logo.svg)

cURL 下载文件的基本命令是
```sh
curl -O https://website.com/path/to/file
```

若要对下载下来的文件重命名，可以用
```sh
curl -o filename https://website.com/path/to/file
```

有些网站需要登录才能访问某些数据。我们可以使用用户名和密码进行认证：
```sh
curl -u username:password -o filename https://website.com/path/to/file
```

cURL 还有许多其他功能，但我们目前只需要这些。

{% note info %}
如果要下载整个站点，最好的选择是递归遍历主页中的所有链接。cURL 不能做到，但是 Wget 可以。用法为
```sh
wget -r http://example.com/
```
但是 Wget 只能处理利用用户名、密码方式限制访问的网站，对于需要证书做认证的网站不适用。
{% endnote %}

# 进行下载

首先在本地磁盘开辟出一块新的地皮，用来存放数据。
```sh
mkdir Dewey && cd Dewey
```
在此处创建脚本（或者你想直接在交互式终端中运行命令也是可以的）。
```sh
touch download.sh
```

脚本内容为：
```sh
#!/bin/zsh
# Set the metadata of data sets
BASEURL="https://marketplace.deweydata.io/api/data/v2/data"
YEAR=${1:-2020}
MONTH=${2:-01}
DATE=${3:-01}
PROVIDER=${4:-SAFEGRAPH}
SUBDIRECTORY="MP"
FID_PREFIX="cpgp_part"
FID_SUFFIX="_0"
FILENAME_PREFIX="core_poi-geometry-patterns-part"
FILENAME_SUFFIX=".csv.gz"

# Set the Authorization info
USER="your@username.com"
PASSWORD="your_password"

# Make directory for the data
mkdir $YEAR$MONTH$DATE-${(L)PROVIDER}
cd $YEAR$MONTH$DATE-${(L)PROVIDER}

# Download
for i in {1..31}
do
	curl -fu $USER:$PASSWORD -o $FILENAME_PREFIX$i$FILENAME_SUFFIX $BASEURL/$YEAR/$MONTH/$DATE/$PROVIDER/$SUBDIRECTORY/$YEAR$MONTH$DATE-${(L)PROVIDER}_${(L)SUBDIRECTORY}_$FID_PREFIX$i$FID_SUFFIX
done
```

脚本接收最多四个有效参数，分别设为年、月、日、数据提供商。不提供参数时，使用默认值 2020, 01, 01, SAFEGRAPH。其他常量可以根据实际需要进行修改。根据 Dewey 存放文件的规律编写 URL，并用 cURL 命令请求下载。认证信息中的用户名和密码修改成自己的。

最新的 SafeGraph 数据分为 31 个 part，因此在循环中循环 31 次进行下载。早期的数据可能没有 31 个 part，这时会输出错误信息，但不会写入文件。

{% note info %}
或者你倾向于使用 Cookies 而不是用户名和密码，你可以将 cURL 的参数 `-fu $USER:$PASSWORD` 改成 `-b "cookies-content"`。
{% endnote %}

运行脚本进行下载。例如，要下载 SafeGraph 2022 年 12 月的数据，运行
```sh
./download.sh 2022 12
```

![](Script-Running.png)

如果提示 `zsh: permission denied: ./download.sh`，说明脚本文件不具有可执行权限。运行以下命令赋予执行权限，然后重新运行脚本。
```sh
chmod +x download.sh
```