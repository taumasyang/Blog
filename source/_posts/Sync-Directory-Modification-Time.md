---
title: 同步目录的修改日期
date: 2025-06-16 14:45:00 -07
updated: 2025-06-16 14:45:00 -07
categories: 实用工具
tags: Shell
index_img: https://www.intego.com/mac-security-blog/wp-content/uploads/2021/03/downloads-hero.png
banner_img: https://4kwallpapers.com/images/wallpapers/macos-big-sur-apple-layers-fluidic-colorful-wwdc-stock-4096x2304-1455.jpg
---

在大多数文件系统上，一份文件的属性可能包含四个时间戳，分别是：

- **访问时间（Access）**：记录了文件最后一次被读取的时间；
- **修改时间（Modify）**：记录了内容文件最后一次被修改的时间；
- **变更时间（Change）**：记录了文件元数据最后一次被修改的时间；
- **创建时间（Birth）**：记录了文件被创建的时间。

```console
% stat -x iCloud
  File: "iCloud"
  Size: 44           FileType: Symbolic Link
  Mode: (0755/lrwxr-xr-x)         Uid: (  501/tauyoung)  Gid: (   20/   staff)
Device: 1,13   Inode: 848160    Links: 1
Access: Wed Nov 20 18:40:00 2024
Modify: Wed Nov 20 18:40:00 2024
Change: Wed Nov 20 18:40:00 2024
 Birth: Wed Nov 20 18:40:00 2024
```

目录（文件夹）同样具有上述四种时间戳，但它们对于目录的意义却不如文件那么明确。例如，创建或删除目录中的文件会更新该目录的修改时间，但是修改文件的内容却不会更新目录的修改时间；有时，对于深层的子目录做出的修改并不会反映在浅层的父目录上。因此可能出现目录内的文件被频繁地更新，而在父目录看来，子目录的修改时间却显示了一个较旧的时间戳；或者删除了目录中较新的冗余文件后，剩余文件的修改时间都十分久远，而目录却有一个很新的时间戳等等反直觉的效果。

个人偏好的做法是手动维护目录的时间戳，使其与目录内文件、目录保持一致。当文件被修改时，包含该文件的目录及其父目录的时间戳都应相应地进行更新；当文件被删除时，目录的时间戳应当与剩余文件中的最新者保持一致。

## 同步目录的函数

```zsh
setdir () {
	# 同时考虑点开头的文件、使用扩展匹配语法、允许匹配结果为空（否则在空目录会出错）
	setopt dotglob extendedglob nullglob
	recurse () {	# 递归地同步子目录
		for i in $1/*(/)
		do
			recurse $i
		done
		local files=($1/^.DS_Store)
		(($#files)) || (	# 空目录
			rmdir -v $1 || tree -a $1
		) && touch -achmd ${"$(date -Iseconds -r $(stat -f %m $files | sort -r | head -1))"[1,19]} $1
	}
	for i in $@
	do
		recurse $i
	done
	unfunction recurse
	unsetopt dotglob extendedglob nullglob
}
```

要使用上述函数，需要认同几个共识：

1. 点开头的文件在 UNIX 系统中默认是隐藏文件，但它们在反映目录时间上与普通文件同样重要，因此需要纳入考虑。
2. `.DS_Store` 在大多数目录下是垃圾文件，需要事先手动移除；需要自定义访达视图的少部分目录遗留的 `.DS_Store` 文件不会纳入考虑；不应有目录仅包含 `.DS_Store` 文件。
3. 空目录无法从文件中获取修改时间，且一般应当被移除；对于部分应用，空目录的存在是必要的，应当避免在此类目录下使用该函数。
4. 极少数情况下（目录内文件太多、目录层级太深等）可能会出现 Argument list too long 错误，可以手动修改函数改为循环实现，但效率会大幅下降。
