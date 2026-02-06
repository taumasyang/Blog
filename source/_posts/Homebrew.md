---
title: 安装和使用 Homebrew
date: 2022-09-22 10:40:30 +08
updated: 2026-02-06 03:35:00 -06
categories: 实用工具
tags: [Shell, Homebrew]
index_img: /img/Homebrew.png
banner_img: http://beekmanbeergarden.com/wp-content/uploads/2020/10/header_brewing-tips.jpg
---

## Homebrew 是什么？

[Homebrew](https://brew.sh) 是一个**包管理器**，可以在 macOS 和 Linux 上安装一些软件、命令等。Homebrew 也支持在 Linux 上运行。

使用 Homebrew 安装软件只需短短的几个命令，无需再从茫茫互联网上寻找你需要的，也无需担心你安装的软件被恶意更改过。

## 安装 Homebrew

在终端中执行以下命令，并按提示操作。原版安装需要用到 GitHub 上的一些资源，因此可能会安装失败，我们使用镜像源（以上海交大镜像源为例）以加速安装：

```sh
export HOMEBREW_BREW_GIT_REMOTE=https://mirrors.sjtug.sjtu.edu.cn/git/brew.git
export HOMEBREW_CORE_GIT_REMOTE=https://mirrors.sjtug.sjtu.edu.cn/git/homebrew-core.git
export HOMEBREW_BOTTLE_DOMAIN=https://mirror.sjtu.edu.cn/homebrew-bottles/bottles
export HOMEBREW_NO_INSTALL_FROM_API=1
/bin/bash -c "$(curl -fsSL https://git.sjtu.edu.cn/sjtug/homebrew-install/-/raw/master/install.sh)"
```

对于基于 Intel 处理器的 Mac，Homebrew 将会安装至 `/usr/local/Homebrew`；对于基于 Apple Silicon 的 Mac，Homebrew 将会安装至 `/opt/homebrew`。

在 `~/.bash_profile`（如果使用 Bash）或 `~/.zprofile` （如果使用 Zsh）中添加如下语句：

```sh
# Set PATH, MANPATH, etc., for Homebrew.
eval "$(/opt/homebrew/bin/brew shellenv)"
export HOMEBREW_BREW_GIT_REMOTE=https://mirrors.sjtug.sjtu.edu.cn/git/brew.git
export HOMEBREW_CORE_GIT_REMOTE=https://mirrors.sjtug.sjtu.edu.cn/git/homebrew-core.git
export HOMEBREW_BOTTLE_DOMAIN=https://mirror.sjtu.edu.cn/homebrew-bottles/bottles
export HOMEBREW_NO_INSTALL_FROM_API=1
```

如果是基于 Intel 处理器的 Mac，按需修改 `eval` 语句中的路径。Homebrew 目前默认使用 API 进行安装，但是上海交大镜像源目前不支持此方式，所以需要设置不从 API 安装。

最后，运行 `source .zprofile` 或者重启终端来启用上述设置。恭喜你！Homebrew 已经设置完成，现在你可以自由安装多数软件了！

## 使用 Homebrew 安装软件

安装软件使用 `brew install` 命令。但是，在大部分情况下我们不能确定这些软件的包名，因此需要先搜索。例如，我们希望安装媒体转码器 FFmpeg，先搜索这个软件的包名：

```console
% brew search ffmpeg
==> Formulae
ffmpeg                     ffmpeg@2.8 (deprecated)    ffmpeg@6
ffmpeg-full                ffmpeg@4                   ffmpeg@7
ffmpeg2theora              ffmpeg@5                   ffmpegthumbnailer
```

我们注意到 Formulae 里面有若干个与我们想要的匹配的包名，其中@后面跟着的是大版本号，如有必要我们可以安装旧版。我们验证一下它是不是我们想要安装的：

```console
% brew info ffmpeg
==> ffmpeg ✘: stable 8.0.1 (bottled), HEAD
Play, record, convert, and stream select audio and video codecs
https://ffmpeg.org/
Not installed
From: https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/f/ffmpeg.rb
License: GPL-2.0-or-later
==> Dependencies
Build: pkgconf ✘
Required: dav1d ✘, lame ✘, libvpx ✘, opus ✘, sdl2 ✘, svt-av1 ✘, x264 ✘, x265 ✘
==> Options
--HEAD
	Install HEAD version
==> Caveats
ffmpeg-full includes additional tools and libraries that are not included in the regular ffmpeg formula.
==> Downloading https://formulae.brew.sh/api/formula/ffmpeg.json
==> Analytics
install: 172,993 (30 days), 408,928 (90 days), 1,552,690 (365 days)
install-on-request: 147,508 (30 days), 354,630 (90 days), 1,370,224 (365 days)
build-error: 3,452 (30 days)
```

Homebrew 给出了这个软件的基本信息、相关依赖和统计信息。我们确定我们想要的就是这个，接下来执行安装命令：

```sh
brew install ffmpeg
```

Homebrew 便会自动下载并安装 FFmpeg 和它所有的依赖。

如果你想卸载 FFmpeg，只需执行：

```sh
brew uninstall ffmpeg
```

对于一些使用安装器（`.pkg`）的软件，在安装和卸载的过程中可能会要求输入密码。

## 卸载 Homebrew

如果你想卸载或重新安装 Homebrew，请执行以下命令以卸载 Homebrew。注意，这也会移除你使用 Homebrew 安装过的 Formula（已安装至 `/Applications` 的应用程序不受影响）！你可以保存 `brew list` 的结果以便再次安装这些包。

```sh
/bin/bash -c "$(curl -fsSL https://git.sjtu.edu.cn/sjtug/homebrew-install/-/raw/master/uninstall.sh)"
```

如果提示有一些文件未被删除，请手动删除它们。

```sh
sudo rm -rf /opt/homebrew	# Apple Silicon
sudo rm -rf /usr/local/*	# Intel
```

如果是基于 Intel 处理器的 Mac，只需将上面这条命令中的目录替换成前文所述的地址即可。
