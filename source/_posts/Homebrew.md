---
title: 安装和使用 Homebrew
date: 2022-09-22 10:40:29
categories: 知识分享
tags: Homebrew
---

## Homebrew 是什么？

[Homebrew](https://brew.sh) 是一个**包管理器**，可以在 macOS 上安装一些软件、命令等。

Homebrew 不会将文件安装到它本身目录之外，安装后只会将软件和命令**软链接**到 `/Application` 和其他存放命令到路径下。

使用 Homebrew 安装软件只需短短的几个命令，无需再从茫茫互联网上寻找你需要的，也无需担心安装的软件是被修改后的版本。

## 安装 Homebrew

在终端中执行以下命令，并按提示操作。如果安装失败，尝试再次运行：

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

对于基于 Intel 处理器的 Mac，Homebrew 将会安装至 `/usr/local/homebrew` 下；对于基于 Apple Silicon 的 Mac，Homebrew 将会安装至 `/opt/homebrew` 下。

成功安装 Homebrew 后，为使更新更快，可以考虑更换镜像源。以上海交大镜像源为例，执行以下命令：

```sh
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.sjtug.sjtu.edu.cn/git/brew.git"
brew update
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.sjtug.sjtu.edu.cn/git/homebrew-core.git"
for tap in core cask{,-fonts,-drivers,-versions}; do
	brew tap --custom-remote --force-auto-update "homebrew/${tap}" "https://mirrors.sjtug.sjtu.edu.cn/git/homebrew-${tap}.git"
done
brew update
```

恭喜你！Homebrew 已经设置完成，现在你可以自由安装大多数软件了！

## 安装软件

安装软件使用 `brew install` 命令。但是，在大部分情况下我们不知道这些软件的包名，因此需要先搜索。例如，我们希望安装视频播放器 IINA，先搜索这个软件的包名：

```sh
brew search iina
```

Homebrew 会给出搜索结果：

```text
==> Formulae
inja

==> Casks
iina                                     iina-plus
```

我们注意到 Casks 里面有个与我们想要的完全匹配的包名，我们验证一下它是不是我们想要安装的：

```sh
brew info iina
```
```text
==> iina: 1.3.0 (auto_updates)
https://iina.io/
Not installed
From: https://mirrors.sjtug.sjtu.edu.cn/git/homebrew-cask.git
==> Name
IINA
==> Description
Free and open-source media player
==> Artifacts
IINA.app (App)
/Applications/IINA.app/Contents/MacOS/iina-cli -> iina (Binary)
==> Analytics
install: 2,420 (30 days), 7,771 (90 days), 30,179 (365 days)
```

我们确定我们想要的就是这个，接下来执行安装命令：

```sh
brew install iina
```
```text
Running `brew update --auto-update`...
==> Auto-updated Homebrew!
Updated 4 taps (homebrew/cask-versions, homebrew/core, homebrew/cask and homebrew/cask-drivers).

==> Downloading https://github.com/iina/iina/releases/download/v1.3.0/IINA.v1.3.0.dmg
######################################################################## 100.0%
==> Installing Cask iina
==> Moving App 'IINA.app' to '/Applications/IINA.app'
==> Linking Binary 'iina-cli' to '/opt/homebrew/bin/iina'
🍺  iina was successfully installed!
```

如果你想卸载 IINA，只需执行：

```sh
brew uninstall iina
```
```text
==> Uninstalling Cask iina
==> Backing App 'IINA.app' up to '/opt/homebrew/Caskroom/iina/1.3.0/IINA.app'
==> Removing App '/Applications/IINA.app'
==> Unlinking Binary '/opt/homebrew/bin/iina'
==> Purging files for version 1.3.0 of Cask iina
```

对于一些使用安装器（`.pkg`）的软件，在安装和卸载的过程中可能会要求输入密码。

## 卸载 Homebrew

如果你想卸载或重新安装 Homebrew，请执行以下命令以卸载 Homebrew。注意，这也会卸载你使用 Homebrew 安装过的包！你可以保存 `brew list` 的结果以便再次安装这些包。

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"
```

如果提示有一些文件未被删除，请手动删除它们。