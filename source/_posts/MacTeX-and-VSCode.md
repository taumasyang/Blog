---
title: MacTeX 配合 Visual Studio Code 进行 LaTeX 写作
date: 2022-09-22 20:41:30
updated: 2023-066-17 19:17:40
categories: 技术分享
tags: LaTeX
---

TexLive 在 macOS 上的发行版是 MacTeX，若使用官方提供的安装包进行安装，还会一并附赠若干我们碰都不会碰的 GUI（图形用户界面）软件用来编辑 $\TeX$ 源文件和管理各类包。本文提供仅安装 MacTeX 核心组件且配置 VSCode 作为 $\LaTeX$ 文件编辑器的详细方案。

# 安装 MacTeX 和 Visual Studio Code

使用 Homebrew 安装上述软件。关于 Homebrew 的安装与使用可以参考我的另一篇文章{% post_link Homebrew %}。
```sh
brew install mactex-no-gui visual-studio-code
```
安装过程中会要求输入密码。

{% note info %}
Homebrew 同时提供了 `mactex` 和 `mactex-no-gui` 两个 Cask，二者都使用从 CTAN 下载的原始安装包，但是后者在安装时会丢弃 GUI 组件。
{% endnote %}

# 更新 MacTeX 宏包

MacTeX 一年更新一次，下载的安装包会一同打包当时版本的宏包。一般来说，距离上次发布已经有很多宏包都经过了更新，所以我们需要重新安装过时的宏包。更新前先更换镜像源以加速下载，以上海交大源为例：
```sh
sudo tlmgr option repository https://mirrors.sjtug.sjtu.edu.cn/ctan/systems/texlive/tlnet
```
更新 MacTeX 包：
```sh
sudo tlmgr update --self --all
```
执行 `sudo` 命令会要求输入密码。

# 配置 Visual Studio Code

首先确保 $\LaTeX$ 的安装目录已经添加至 `PATH`。用 Homebrew 安装的 MacTeX 会自动添加至 `PATH` 中，如果没有，则需要手动添加。安装 MacTeX 后需要重启终端或者执行 `eval "$(/usr/libexec/path_helper)"` 来使路径生效。
```sh
type xelatex
```
如果有显示如下输出，代表系统可以找到 $\LaTeX$ 编译器。
```text
xelatex is /Library/TeX/texbin/xelatex
```

{% note info %}
我们使用 XeLaTeX 是因为它支持 Unicode 编码，可以编译中文文档。其他编译器大多只支持英文或不能访问系统的字体库，在一些情况下可能会无法正常编译。此外，就笔者个人而言，即使是全 ASCII 字符的文章，XeLaTeX 的编译结果也比 pdfLaTeX 好看。
{% endnote %}

接下来，打开 VSCode，搜索并安装 LaTeX Workshop 插件。

![LaTeX Workshop](/img/LaTeX_Workshop.png)

最后，在 VSCode 配置文件 `settings.json` 的合适位置添加以下内容：
```json
"latex-workshop.latex.tools": [
	{
		"name": "latexmk",
		"command": "latexmk",
		"args": [
			"-synctex=1",
			"-interaction=nonstopmode",
			"-file-line-error",
			"-pdf",
			"%DOC%"
		]
	},
	{
		"name": "xelatex",
		"command": "xelatex",
		"args": [
			"-synctex=1",
			"-interaction=nonstopmode",
			"-file-line-error",
			"%DOCFILE%"
		]
	},
	{
		"name": "pdflatex",
		"command": "pdflatex",
		"args": [
			"-synctex=1",
			"-interaction=nonstopmode",
			"-file-line-error",
			"%DOC%"
		]
	},
	{
		"name": "bibtex",
		"command": "bibtex",
		"args": [
			"%DOCFILE%"
		]
	}
],
"latex-workshop.latex.recipes": [
	{
		"name": "xelatex",
		"tools": [
			"xelatex"
		]
	},
	{
		"name": "latexmk 🔃",
		"tools": [
			"latexmk"
		]
	},
	{
		"name": "pdflatex ➞ bibtex ➞ pdflatex ×2",
		"tools": [
			"pdflatex",
			"bibtex",
			"pdflatex",
			"pdflatex"
		]
	},
	{
		"name": "xelatex ➞ bibtex ➞ xelatex ×2",
		"tools": [
			"xelatex",
			"bibtex",
			"xelatex",
			"xelatex"
		]
	}
],
"latex-workshop.latex.clean.fileTypes": [
	"*.aux",
	"*.bbl",
	"*.blg",
	"*.idx",
	"*.ind",
	"*.ilg",
	"*.lof",
	"*.lot",
	"*.out",
	"*.toc",
	"*.acn",
	"*.acr",
	"*.alg",
	"*.glg",
	"*.glo",
	"*.gls",
	"*.fls",
	"*.log",
	"*.fdb_latexmk",
	"*.snm",
	"*.nav",
	"*.vrb"
],
"latex-workshop.latex.clean.method": "glob"
```

如果设置了自动保存和自动编译，那么在源文件修改后可以近乎实时地看到编译结果，在享受 $\LaTeX$ 强大排版能力的同时还能做到其他文字排版工具的「所见即所得」，不失为一种享受。不过，如果你的机器性能不太够，或者文件内包含大尺寸图片或者大量 TikZ 画图会严重拖慢编译速度，还是建议关闭自动编译功能。