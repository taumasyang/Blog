---
title: Zsh 常用命令速查
tags:
---

删除当前目录及其子目录下的所有 `.DS_Store` 文件
```sh
find . -name ".DS_Store" -type f -delete
```
删除所有隐藏 `.DS_store` 文件
```sh
sudo find / -name ".DS_Store" -depth -exec rm {} \;
```
重置LaunchPad
```sh
defaults write com.apple.dock ResetLaunchPad -bool true; killall Dock
```
修改文件创建日期
```sh
setfile -d 'mm/dd/yy hh:mm:ss' filename
```
修改文件修改日期
```sh
setfile -m 'mm/dd/yy hh:mm:ss' filename
```