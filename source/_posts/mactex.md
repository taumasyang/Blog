---
title: MacTeX 配合 Visual Studio Code 进行 LaTeX 写作
date: 2022-09-22 20:40:00
categories: 技术分享
tags: LaTeX
---

## 安装 MacTeX 和 Visual Studio Code

使用 Homebrew 安装上述软件。关于 Homebrew 的安装与使用可以参考我的另一篇文章 {% post_link homebrew '安装和使用 Homebrew' %}。

```zsh
brew install mactex-no-gui visual-studio-code
```

安装过程中会要求输入密码。

{% note info %}
Homebrew 同时提供了 `mactex` 和 `mactex-no-gui` 两个 Cask，从 MacTeX 官方网站上下载安装的是 `mactex` 包，并且安装时会一并安装官方提供的 LaTeX 编辑器和包管理器。我们并不使用官方提供的这些 GUI 工具，因此安装 `mactex-no-gui` 可以避免安装这些无用的工具。不过不用担心，我们更新 LaTeX 包所使用的命令行工具仍然是官方所提供的 `tlmgr`。
{% endnote %}

## 更新 MacTeX 包

更新前先更换镜像源，以上海交大源为例：

```zsh
sudo tlmgr option repository https://mirrors.sjtug.sjtu.edu.cn/ctan/systems/texlive/tlnet
```

更新 MacTeX 包。

```zsh
sudo tlmgr update --self --all
```

执行 `sudo` 命令会要求输入密码。

## 配置 Visual Studio Code

首先确保 LaTeX 的安装目录已经添加至 `PATH`。用 Homebrew 安装的 MacTeX 会自动添加至 `PATH` 中，如果没有，则需要手动添加。

```zsh
type xelatex
```

如果有显示如下输出，代表系统可以直接调用 LaTeX 进行编译。

```console
xelatex is /Library/TeX/texbin/xelatex
```

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

大功告成！快去试试用 LaTeX 写一篇文章吧！
