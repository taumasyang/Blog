---
title: 一些有用的 Shell 命令
date: 2023-03-20 11:50:00 +08
updated: 2023-03-20 11:50:00 +08
categories: 实用工具
tags: Shell
index_img: https://www.grandream.jp/image/blog/zsh.png
banner_img: https://images.pexels.com/photos/1013480/pexels-photo-1013480.jpeg
---

本文收录了一些我用过的觉得今后还能用上 Shell 命令，作为存档，不定期更新。

## 系统相关

锁定 Dock 高度

```zsh
defaults write com.apple.Dock size-immutable -bool yes; killall Dock
```

重置 LaunchPad

```zsh
defaults write com.apple.dock ResetLaunchPad -bool true; killall Dock
```

## 文件与文件夹操作

修改文件日期

```zsh
touch -achmt [[CC]YY]MMDDhhmm[.SS] filename
```

修复损坏 App

```zsh
sudo xattr -rd com.apple.quarantine /Applications/AppName.app
```

符号链接

```zsh
ln -s /path/to/actual/file /path/to/linked/file
```

打包并加密

```zsh
tar -czf - /path/to/directory | openssl enc -aes-256-cbc -salt -out filename.tar.gz.enc
```

解密

```zsh
openssl enc -aes-256-cbc -d -in filename.tar.gz.enc | tar -xzf -
```

### 文件转换

导出 Markdown 为 PDF

```zsh
pandoc -s --pdf-engine=xelatex -V CJKmainfont='Songti SC' -V geometry:margin=1in filename.md -o filename.pdf
```

压制视频

```zsh
ffmpeg -i input.mov -s 1920x1080 -c:v hevc -crf 23 -metadata:s language=zho output.mp4
```

## 批处理

批量转换文件

```zsh
for f in *.flac; do ffmpeg -i $f -c:a alac ${f:r}.m4a; done
```

## 特定程序相关

修改 TeX Live 镜像源

```zsh
sudo tlmgr option repository https://mirrors.sjtug.sjtu.edu.cn/ctan/systems/texlive/tlnet
```

修改 `pip` 镜像源

```zsh
pip config set global.index-url https://mirror.sjtu.edu.cn/pypi/web/simple
```

禁止 `conda` 自动激活 `base` 环境

```zsh
conda config --set auto_activate_base False
```
