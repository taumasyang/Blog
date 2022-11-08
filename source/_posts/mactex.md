---
title: MacTeX 配合 Visual Studio Code 进行 LaTeX 写作
date: 2022-09-22 20:40:00
categories: 技术分享
tags: LaTeX
---

## 安装 MacTeX 和 Visual Studio Code

使用 Homebrew 安装上述软件。关于 Homebrew 的安装与使用可以参考我的另一篇文章{% post_link homebrew %}。

```zsh
brew install mactex-no-gui visual-studio-code
```

安装过程中会要求输入密码。

{% note info %}
Homebrew 同时提供了 `mactex` 和 `mactex-no-gui` 两个 Cask，从 MacTeX 官方网站上下载安装的是 `mactex` 包，安装时会一并安装官方提供的 LaTeX 编辑器和包管理器。如果不使用官方提供的这些 GUI 工具，可以选择安装 `mactex-no-gui`。
{% endnote %}

## 更新 MacTeX 宏包

MacTeX 每年更新一次，下载的安装包会一同打包当时版本的宏包。一般来说，距离上次发布已经有很多宏包都经过了更新，所以我们需要重新安装过时的宏包。更新前先更换镜像源以加速下载，以上海交大源为例：

```zsh
sudo tlmgr option repository https://mirrors.sjtug.sjtu.edu.cn/ctan/systems/texlive/tlnet
```

更新 MacTeX 包。

```zsh
sudo tlmgr update --self --all
```

执行 `sudo` 命令会要求输入密码。

## 配置 Visual Studio Code

首先确保 LaTeX 的安装目录已经添加至 `PATH`。用 Homebrew 安装的 MacTeX 会自动添加至 `PATH` 中，如果没有，则需要手动添加。安装 MacTeX 后需要重启终端或者执行 `eval "$(/usr/libexec/path_helper)"` 来使路径生效。

```zsh
type xelatex
```

如果有显示如下输出，代表系统可以找到 LaTeX 编译器。

```console
xelatex is /Library/TeX/texbin/xelatex
```

{% note info %}
我们使用 `xelatex` 是因为它支持 Unicode 编码，可以编译中文文档。其他编译器大多只支持西文或不能访问系统的字体库，在一些情况下可能会无法正常编译。
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
```

如果设置了自动保存和自动编译，那么在源文件修改后可以近乎实时地看到编译结果，在享受 $\LaTeX$ 强大排版能力的同时还能做到其他文字排版工具的「所见即所得」，不失为一种享受。不过，如果你的机器性能不太够，还是建议关闭自动编译功能。
