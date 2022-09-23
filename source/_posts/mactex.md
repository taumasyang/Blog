---
title: MacTeX 配合 Visual Studio Code 进行 LaTeX 创作
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

## 更新 MacTeX 包

更新前先更换镜像源，以上海交大源为例：

```zsh
sudo tlmgr option repository https://mirrors.sjtug.sjtu.edu.cn/ctan/systems/texlive/tlnet
```

更新 MacTeX 包。

```zsh
sudo tlmgr update --self --all
```

## 配置 Visual Studio Code

首先确保 LaTeX 的安装目录已经添加至 `PATH`。

```zsh
where latex
```

如果有显示路径，代表系统可以直接调用 LaTeX 进行编译。接下来，打开 VSCode，搜索并安装 LaTeX Workshop 插件。

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
