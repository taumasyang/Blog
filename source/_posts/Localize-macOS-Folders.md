---
title: 本地化 macOS 目录
date: 2024-03-25 23:30:00
updated: 2024-03-25 23:30:00
categories: 实用工具
tags: [macOS, Shell]
index_img: https://pic2.zhimg.com/v2-625173db197b64e4103e36da9340b594_1440w.jpg
banner_img: https://publitrad.ro/wp-content/uploads/2022/05/What-is-localization-and-why-is-it-so-important-for-businesses.jpg
---

通常，在 macOS「访达」中查看个人目录，会显示「文稿」「桌面」「下载」等本地化的名字，而在「终端」中查看，则会发现这些目录的本名还是「Documents」「Desktop」「Downloads」等等。在某些不常见的情况下，我们使用 `rm -rf ~/Desktop/.*` 命令清理这些目录下所有隐藏文件时，本地化的「桌面」目录又回现出原名「Desktop」。我们上网搜索一下解决方案，发现只需要我们在对应目录下创建一个 `.localized` 文件即可恢复。

由此可见，macOS 下目录名的本地化似乎是通过 `.localized` 文件完成的。但请注意，此处我们仅仅只是创建了一个空白的隐藏文件，既没有写入任何本地化的信息，又适用于个人目录下的所有系统目录，这是怎么做到的呢？

事实上，在 `/System/Library/CoreServices/SystemFolderLocalizations/` 目录下，存在各种语言的本地化资料，例如简体中文的对应名字就存放在 `zh_CN.lproj/SystemFolderLocalizations.strings` 里。因此，一个空的 `.localized` 文件即可告诉 macOS 此目录需要本地化，然后系统会从上述 `strings` 文件里查找对应的名字，并用本地化的字符进行显示。

有时我们会遇到这样的需求，我们希望让一个目录能够像系统目录一样，在访达中显示为本地化的名称，而在终端中又是纯英文字符方便通过命令进行操作。很可惜，系统的 `strings` 文件仅仅包含了系统会使用到的名称对照，并不能实现任意目录的本地化。当然通过修改系统的 `strings` 文件，然后在对应目录下创建空的 `.localized` 文件就能做到像系统目录一样本地化。然而，该 `strings` 文件是二进制 `plist` 格式的，无法通过文本编辑器进行修改。而且这种修改与机器相关联，一旦该目录被发送到另外一台机器上，本地化就会失效。进一步讲，修改系统文件是非常危险的行为，因此尽管可以，但是十分不推荐。

相对而言，还有一种更为方便且安全的做法，能够让本地化信息保存到需要被本地化的目录内，不仅不需要破坏系统，整个目录放到新的机器上也能正常显示。为了让 macOS 能够显示本地化的名称，需要满足：

1. 目录名以 `.localized` 结尾，例如 `Folder.localized`；
2. 目录内有一个名为 `.localized` 的子目录，即 `Folder.localized/.localized`；
3. 在 `.localized` 子目录下，需要存在于当前系统语言相符的 `strings` 文件，简体中文为 `zh_CN.strings`；
4. 在 `strings` 文件内，填写本地化信息，例如 `"Folder" = "文件夹";`，注意最后的分号不可省略。

整个过程是非常程序化的，因此如果需要本地化大量目录，写一个脚本来实现是非常便利的。这个脚本接受两个参数，需要本地化的目录以及本地化后的名称。假设本地化为简体中文，则脚本的内容如下：

```sh
sini()
{
	[[ -d $1 ]] && dir=$(basename $1) || return 1
	[[ -z $2 ]] && return 2
	mkdir "$1/.localized"
	echo "\"$dir\" = \"$2\";" > "$1/.localized/zh_CN.strings"
	mv "$1" "$1.localized"
}
```

加载此函数后，运行类似于 `sini Folder 文件夹` 的命令即可快速本地化。若目录不存在或本地化参数为空，函数会错误退出，不会进行任何修改。该函数也可在脚本中使用。
