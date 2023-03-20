---
title: 一些有用的 Shell 命令
date: 2023-03-20 11:50:00
categories: 技术分享
tags: Shell
---
本文收录了一些我用过的觉得今后还能用上 Shell 命令，作为存档，不定期更新。
## 系统相关
### 锁定Dock高度
```sh
defaults write com.apple.Dock size-immutable -bool yes; killall Dock
```
### 重置LaunchPad
```sh
defaults write com.apple.dock ResetLaunchPad -bool true; killall Dock
```
## 文件转换
### 导出 Markdown 为 PDF
```sh
pandoc -s --pdf-engine=xelatex -V CJKmainfont='STSong' -V geometry:margin=1in filename.md -o filename.pdf
```
### 压制视频
```sh
ffmpeg -i input.mov -s 1920x1080 -c:v hevc -crf 23 -metadata:s language=zho output.mp4
```
## 批处理
### 删除 .DS_Store 文件
```sh
find . -name ".DS_Store" -type f -delete
```
### 批量转换文件
```sh
for f in *.flac; do ffmpeg -i "$f" -c:a alac "${f%.*}.m4a"; done
```
## 其他
### 修改文件日期
```sh
touch -achmt [[CC]YY]MMDDhhmm[.SS] filename
```
### 修复损坏 App
```sh
sudo xattr -rd com.apple.quarantine /Applications/AppName.app
```
### 生成软链接
```sh
ln -s /path/to/actual/file /path/to/linked/file
```