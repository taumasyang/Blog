---
title: 必应每日一图？我收下了！
date: 2023-12-11 13:30:00 +08
updated: 2026-04-13 18:30:00 -05
categories: 实用工具
tags: [Shell, Git]
index_img: https://cdn.wallpaperhub.app/cloudcache/3/c/a/e/9/4/3cae9423e2f818afb6e64a220ea2c39fd0cee877.jpg
banner_img:
---

微软必应的每日一图收录了来自全世界的自然风光和人文摄影。这些唯美的照片不仅是 bing.com 的默认背景，微软也官方提供了「[必应壁纸](https://www.microsoft.com/zh-cn/bing/bing-wallpaper)」应用供用户将其设为桌面。

这些图片质感极佳，我很喜欢，于是决定用脚本把它们每天自动保存下来，并提取最新的一张作为本站的首页头图。

[![](https://img.shields.io/badge/GitHub-imageArchive-0?logo=GitHub)](https://github.com/taumasyang/imageArchive)

## 探秘必应每日一图 API

必应每日一图的元数据可以通过 https://www.bing.com/HPImageArchive.aspx 获取。直接访问该地址不会返回任何有效内容，我们需要为其追加查询参数：

- `format`：返回数据的格式。可选：`js`，`xml`（默认），`rss`。
- `idx`（必需）：图片日期相对今天的偏移量。可选：0-7（大于 7 按 7 处理）。
- `n`（必需）：获取的图片数量。可选：1-8（大于 8 按 8 处理）。
- `mkt`：地区代码，大小写不敏感。不同地区的每日一图可能有所不同。

{% note danger %}
中国大陆的 IP 直接请求只能获取中国大陆（`ZH-CN`）的结果，`mkt`参数会失效。如果需要获取全球其他地区的图片，需要配置网络代理。
{% endnote %}

经过多次测试，`mkt`取以下值时能基本覆盖必应全球每日更新的图片库：`EN-US` `JA-JP` `ZH-CN` `EN-IN` `DE-DE` `ES-ES` `FR-FR` `IT-IT` `EN-GB` `PT-BR` `EN-CA`。

接下来，我们发一条 GET 请求看看返回结构：

https://www.bing.com/HPImageArchive.aspx?idx=0&n=1&mkt=zh-cn

```xml
<images>
	<image>
		<startdate>20231230</startdate>
		<fullstartdate>202312300800</fullstartdate>
		<enddate>20231231</enddate>
		<url>/th?id=OHR.ThailandNewYears_ZH-CN2058192262_1920x1080.jpg&rf=LaDigue_1920x1080.jpg&pid=hp</url>
		<urlBase>/th?id=OHR.ThailandNewYears_ZH-CN2058192262</urlBase>
		<copyright>帕那空奇里上空的烟花，碧武里府,泰国 (© noomcpk/Shutterstock)</copyright>
		<copyrightlink>https://www.bing.com/search?q=%E8%B7%A8%E5%B9%B4%E5%A4%9C&form=hpcapt&mkt=zh-cn</copyrightlink>
		<headline>明年见!</headline>
		<drk>1</drk>
		<top>1</top>
		<bot>1</bot>
		<hotspots />
	</image>
	<tooltips>
		<!-- 这部分省略 -->
	</tooltips>
</images>
```

分析 XML 数据，提取我们需要的部分：

- `enddate`：图片实际发布的日期。
- `urlBase`：图片的基础路径。我们可以基于它拼接出不同分辨率的图片地址（例如追加`_1920x1080.jpg`）。同时，这个字符串里还藏着图片的主题（如`ThailandNewYears`）。
- `copyright`：图片的拍摄位置与版权信息。
- `headline`：图片的简短描述。

基于`urlBase`，我们只需向 https://www.bing.com/th?id=OHR.ThailandNewYears_ZH-CN2058192262_1920x1080.jpg 发送请求，就能直接拿到这张图片了。

![新年快乐！](https://www.bing.com/th?id=OHR.ThailandNewYears_ZH-CN2058192262_1920x1080.jpg)

## 编写 Zsh 自动化脚本

手动抓取不仅繁琐，还容易遗漏。为了实现真正的自动化，我们需要编写一个 Zsh 脚本来处理数据提取、去重、下载和归档。

### 遍历全球地区

我们想要的不仅是本地图片，而是全球范围内的优质摄影。因此，我们需要遍历之前整理出的所有地区代码：

```zsh
for mkt in {EN-US,JA-JP,ZH-CN,EN-IN,DE-DE,ES-ES,FR-FR,IT-IT,EN-GB,PT-BR,EN-CA}
do
	# 下一步做什么？
done
```

### 获取并解析元数据

使用 [cURL](https://curl.se/) 获取 XML，再交由`xmllint`通过 XPath 提取所需字段：

```zsh
image=$(curl -sG -d idx=0 -d n=1 -d mkt=$mkt https://www.bing.com/HPImageArchive.aspx)
enddate=$(xmllint --xpath '//enddate/text()' - <<< $image)
urlBase=$(xmllint --xpath '//urlBase/text()' - <<< $image)
headline=$(xmllint --xpath '//headline/text()' - <<< $image)
copyright=$(xmllint --xpath '//copyright/text()' - <<< $image)
```

注：`-s`参数用于静默模式隐藏进度条，`-G`强制使用 GET 请求，`-d`用于拼接查询参数。

### 文件命名与数据沉淀

如何命名下载的图片？`enddate`并不是好选择，因为同一天多地区可能有不同图片，同一图片也可能在不同日期出现在不同地区。最合理的做法是利用`urlBase`中包含的标题。

这里利用 Zsh 内置的字符串截断语法，将`/th?id=OHR.ThailandNewYears_ZH-CN2058192262`中的`ThailandNewYears`剥离出来：

|语法|方向|程度|
|-|-|-|
|`${str#*.}`|删除`.`左侧的内容|最小匹配|
|`${str##*.}`|删除`.`左侧的内容|最大匹配|
|`${str%_*}`|删除`_`右侧的内容|最小匹配|
|`${str%%_*}`|删除`_`右侧的内容|最大匹配|

结合运用，就能精准提取文件名：

```zsh
filename=${${urlBase#*.}%%_*}
```

为了方便日后查阅，我们同时将获取的各项元数据存储在`metadata/`目录下的 CSV 文件中，并将图片下载至`img/`目录：

```zsh
print -n -- "$enddate,$filename,$mkt," >> metadata.csv
print -n -- "$(grep -oE '[0-9]+' <<< ${urlBase##*_})," >> metadata.csv
print -n -- "\"$headline\"," >> metadata.csv
print -- "\"$copyright\"" >> metadata.csv
curl -so img/$filename.jpg https://www.bing.com${urlBase}_1920x1080.jpg
```

![](/img/BingImageMetadata.png)

### 异常处理与防重复下载

网络请求总有失败的可能。如果未获取到`image`数据，强行解析会导致空变量和坏文件。同时，多地区往往存在重复推图的情况。我们需要添加校验逻辑：

```zsh
# 获取不到内容时重试或跳过
while [[ -z $image ]]
do image=$(curl -sG -d idx=0 -d n=1 -d mkt=$mkt https://www.bing.com/HPImageArchive.aspx)
done

# 如果图片已存在，则不再重复下载
[[ -f img/$filename.jpg ]] && continue
```

### 指定「最新图」

因为我的博客首页需要固定读取每日最新图，我们不能每次都修改源码里的图片名称。解决方案是生成一个`latest.jpg`副本。

为了不浪费磁盘空间，这里使用硬链接，让`latest.jpg`和刚下载的图片指向硬盘上的同一物理位置：

```zsh
ln -f img/$filename.jpg img/latest.jpg
```

在推送到 Git 仓库时，Git 仍会将其作为两份独立的文件树进行追踪，但这在本地管理中确实是个优雅的技巧。

## 用 Git LFS 托管至 GitHub

由于图片体积较大且数量会不断增加，我们不推荐直接把图片丢进普通 Git 仓库里，而是使用 Git LFS (Large File Storage)。它将大文件替换为文本指针进行版本控制，不仅不影响拉取代码的速度，GitHub 还免费提供了 10 GiB 的 LFS 额度，绰绰有余。

安装并配置 Git LFS（macOS 使用 Homebrew）：

```zsh
brew install git-lfs
git lfs install
```

告诉 LFS 追踪所有`.jpg`文件，并保存配置规则：

```zsh
git lfs track "*.jpg"
git add .gitattributes
```

最后，只需将`git add`、`commit`和`push`写入我们的脚本末尾，抓图后即可自动推送到 GitHub 仓库。

完成推送后，latest.jpg 在外网的长期有效访问地址就是：

```url
https://media.githubusercontent.com/media/<你的用户名>/<仓库名>/main/img/latest.jpg
```

把这个直链放到博客配置里，就能实现首页图日更了！

![正是本站今日的首页图！](https://media.githubusercontent.com/media/taumasyang/imageArchive/main/img/latest.jpg)

## 最终完整脚本

结合上述所有思路，最终的 Zsh 脚本如下：

```zsh
#!/bin/zsh
# version 1.5.3
local -i idx=${1:-0} n=1
(( idx > 14 )) && print -u2 -- 'index too large' && return $idx
(( idx > 7 )) && (( n = idx - 6 ))
local mkt image
for mkt in {EN-US,JA-JP,ZH-CN,EN-IN,DE-DE,ES-ES,FR-FR,IT-IT,EN-GB,PT-BR,EN-CA}
do
	image=''
	while [[ -z $image ]]
	do image=$(curl -sG -d idx=$idx -d n=$n -d mkt=$mkt https://www.bing.com/HPImageArchive.aspx)
	done
	local enddate=$(xmllint --xpath '//enddate/text()' - <<< $image | head -n$n)
	local urlBase=$(xmllint --xpath '//urlBase/text()' - <<< $image | head -n$n)
	local filename=${${urlBase#*.}%%_*}
	print -- "$enddate,$filename,$mkt,$(grep -oE '[0-9]+' <<< ${urlBase##*_}),\"$(xmllint --xpath '//headline/text()' - <<< $image | head -n$n)\",\"$(xmllint --
xpath '//copyright/text()' - <<< $image | head -n$n)\"" >> metadata/$mkt.csv
	[[ -f img/$filename.jpg ]] || curl -so img/$filename.jpg https://www.bing.com${urlBase}_1920x1080.jpg
	ln -f img/$filename.jpg img/latest.jpg
done
[[ $(git status --porcelain) ]] || return
git add img metadata
git commit -m "Fetch: $enddate"
(($#1)) || git push
```

## 定时自动执行

脚本写好了，最后一步就是让它自动跑起来。这里提供两种方案。

### 方案 A：macOS 本地任务（`launchd`）

如果你有一台经常开机的 Mac，可以使用自带的`launchd`服务。它通过 XML（plist）文件配置。将以下内容保存为`~/Library/LaunchAgents/top.tauyoung.imagearchive.plist`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
	<dict>
		<key>Label</key>
		<string>top.tauyoung.imagearchive</string>
		<key>Program</key>
		<string>/Users/tauyoung/imageArchive/fetch.sh</string>
		<key>EnvironmentVariables</key>
		<dict>
			<key>ALL_PROXY</key>
			<string>socks5://localhost:7890</string>
			<key>PATH</key>
			<string>/opt/homebrew/bin:/opt/homebrew/sbin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin</string>
		</dict>
		<key>WorkingDirectory</key>
		<string>/Users/tauyoung/imageArchive</string>
		<key>StartCalendarInterval</key>
		<dict>
			<key>Hour</key>
			<integer>13</integer>
			<key>Minute</key>
			<integer>0</integer>
		</dict>
		<key>StandardOutPath</key>
		<string>/Users/tauyoung/Library/Logs/top.tauyoung.imagearchive.log</string>
		<key>StandardErrorPath</key>
		<string>/Users/tauyoung/Library/Logs/top.tauyoung.imagearchive.err</string>
	</dict>
</plist>
```

- `<key>Hour</key><integer>13</integer>`表示在本地时间的每日 13:00 触发执行。
- launchd 中必须全部使用绝对路径，不能使用`~`缩写。

加载定时任务：

```zsh
launchctl load ~/Library/LaunchAgents/top.tauyoung.imagearchive.plist
```

## GitHub Actions 云端任务（推荐）

云端执行不受本机休眠状态影响，更重要的是 GitHub Actions 自带海外网络环境，完美绕过了拉取其他国家／地区图片时的网络限制。

在仓库的根目录下创建`.github/workflows/github-fetch.yml`：

```yml
name: Fetch

on:
  schedule:
	- cron: '0 5 * * *'

permissions:
  contents: write

jobs:
  fetch:
	runs-on: macos-latest
	steps:
	  - name: Checkout
		uses: actions/checkout@v6
	  - name: Configure Git
		run: |
		  git config user.name <user.name>
		  git config user.email <user.email>
	  - name: Fetch
		run: ./fetch.zsh
```

保存并推送后，GitHub 就会在每天按时为你打工收图了。一切就是这么简单！
