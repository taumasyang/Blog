---
title: Git 与 GitHub 使用指北
date: 2022-11-01 14:40:10
updated: 2023-06-24 06:12:10
categories: 技术分享
tags:
  - Shell
  - Git
---

# 源代码管理

在一个项目开发的过程中，我们通常会经历许多不同的版本。特别是多人协作开发的时候，每个人负责不同的部分，最后需要把每个人的代码合并起来。或者在某个版本出现的代码，下一个版本中被删除了，而后续又需要把它加回来，等等。这些不同的需求促使我们从项目一开始就做好源代码管理。本文主要介绍源代码管理工具 Git 和代码托管平台 GitHub 的使用。

[Git](https://git-scm.com/) 是用于 Linux 内核开发的版本控制工具。与 CVS、Subversion 一类的集中式版本控制工具不同，它采用了分布式版本库的作法，不需要服务器端软件，就可以运作版本控制，使得源代码的发布和交流极其方便。Git 的速度很快，这对于诸如 Linux 内核这样的大项目来说自然很重要。Git 最为出色的是它的合并追踪能力。[^1]

[GitHub](https://github.com/) 是一个在线软件源代码托管服务平台，使用 Git 作为版本控制软件，由开发者 Chris Wanstrath、P. J. Hyett 和 Thomas Preston-Werner 使用 Ruby on Rails 编写而成。在 2018 年，GitHub 被微软公司收购。[^2]

# 配置 GitHub

## 创建 GitHub 账号

访问 [GitHub](https://github.com/)。

![GitHub](/img/GitHub.png)

{% note warning %}
GitHub 在国内访问并不稳定，重度用户一般使用科学上网的手段保证连接的稳定性。
{% endnote %}

点击 [Sign up](https://github.com/signup)，输入电子邮箱，然后按指示注册账号。

## 使用 SSH 连接 GitHub[^3]

### 检查现有的 SSH 密钥

在你生成一个新的 SSH 密钥之前，你应该检查你的本地机器是否存在密钥。

打开终端，输入 `ls -al ~/.ssh` 查看现有的 SSH 密钥是否存在。
```sh
$ ls -al ~/.ssh
# 列出你的 .ssh 目录中的文件，如果它们存在的话
```
检查目录列表，看看你是否已经有一个公共的 SSH 密钥。默认情况下，支持 GitHub 的公钥的文件名是以下之一。
- `id_rsa.pub`
- `id_ecdsa.pub`
- `id_ed25519.pub`

{% note info %}
提示：如果你收到 `~/.ssh` 不存在的错误，你在默认位置没有现有的 SSH 密钥对。你可以在下一步创建一个新的 SSH 密钥对。
{% endnote %}

要么生成一个新的SSH密钥，要么上传一个现有的密钥。

如果你没有支持的公钥和私钥对，或者不希望使用任何可用的公钥和私钥对，那么就生成一个新的SSH密钥。

如果你看到一个现有的公钥和私钥对（例如，`id_rsa.pub` 和 `id_rsa`），你想用它来连接 GitHub，你可以把这个密钥添加到 `ssh-agent`。

### 生成一个新的 SSH 密钥

检查完现有的 SSH 密钥后，你可以在你的本地机器上生成一个新的 SSH 密钥。生成密钥后，你可以将密钥添加到你在 GitHub.com 上的账户，以便通过 SSH 对 Git 操作进行认证。

打开终端，粘贴下面的命令，并将电子邮件替换成你的 GitHub 电子邮件地址。
```
ssh-keygen -t ed25519 -C "your_email@example.com"
```
这将创建一个新的 SSH 密钥，使用提供的电子邮件作为标签。

当你被提示“输入一个保存密钥的文件”时，你可以按回车键来接受默认的文件位置。请注意，如果你以前创建过 SSH 密钥，`ssh-keygen` 可能会要求你重写另一个密钥，在这种情况下，我们建议创建一个自定义命名的 SSH 密钥。要做到这一点，请输入默认的文件位置，并用你的自定义密钥名称替换 `id_ssh_keyname`。

生成完毕，执行 `cat ~/.ssh/id_ed25519.pub` 来查看公钥。将输出的结果拷贝至剪贴板中。

### 在 GitHub 上添加密钥

回到 GitHub 网站，点击右上角头像，选择「Settings」进入设置页面，然后在左边边栏的「Access」栏找到「SSH and GPG Keys」。

选择「New SSH Key」，指定一个标题，并将刚才拷贝的公钥粘贴至下方的文本框中。

![GitHub New SSH Key](/img/GitHub-New-SSH-Key.png)

点击「Add SSH Key」，将验证密码。操作成功后，就将本机与 GitHub 连接了。

{% note warning %}
如果你需要在不同的电脑上操作同一仓库，你需要在每台电脑上都执行上述操作。
{% endnote %}

# 配置 Git

macOS 系统已经自动安装了 Git，所以你无需手动安装。如果你不放心，可以运行 `git --version` 来确认 Git 是否已经安装以及 Git 的版本。在笔者的机器上输出结果如下：
```txt
git version 2.37.0 (Apple Git-136)
```
但是，你仍然需要配置用户名和电子邮箱才能使用它。运行以下命令，将 `username` 和 `username@example.com` 替换为你的信息。
```sh
git config --global user.name username
git config --global user.email username@example.com
```
用这种方式也可以配置 Git 的其他行为。

# 创建仓库

## 在 GitHub 上创建仓库

在 GitHub 主页找到「New Repository」按钮，点击它将来到一个新页面。

![GitHub New Repository](/img/GitHub-New-Repository.png)

在这个页面，你需要为你的新仓库指定一些信息。

- Repository name 仓库名称：优秀的仓库名称是简短而令人难忘的。
- Description (optional) 描述（可选）。
- Visibility 可见性：
	- 公开：互联网上的任何人都可以看到这个存储库。你选择谁可以提交。
	- 私有：你可以选择谁可以看到和提交这个版本库。
- Add a README file 添加 README 文件：在这里，你可以为你的项目写一个长的描述。
- Add .gitignore 添加 .gitignore：从一个模板列表中选择哪些文件不需要跟踪。
- Choose a license 选择一个许可证：许可证告诉别人他们可以和不可以用你的代码做什么。

{% note warning %}
如果要导入一个已存在的仓库，不要修改最后三项。
{% endnote %}

确认好信息后，点击「Create Repository」，就可以进入你刚刚创建的仓库啦。

## 将仓库克隆到本地

在仓库页面，寻找绿色的按钮「Code」，选择「SSH」，将仓库地址拷贝至剪贴板中。

回到本机，先在终端中 `cd` 到你想存放这个仓库的目录，运行 `git clone` 命令克隆仓库。
```sh
git clone git@github.com:username/repository.git
```
然后 `cd` 到仓库目录。
```sh
cd repository
```
以后对这个仓库进行 `git` 操作都需要在这个目录下执行。

## 在本地创建仓库

除了在 GitHub 上创建仓库后克隆到本地，你也可以选择在本地创建仓库后上传到 GitHub。

先在终端中 `cd` 到你想存放这个仓库的目录，运行 `git init` 命令初始化仓库。
```sh
git init repository
```
然后 `cd` 到仓库目录。
```sh
cd repository
```
同样地，以后对这个仓库进行 `git` 操作都需要在这个目录下执行。

## 将仓库上传到 GitHub

远程创建 GitHub 仓库需要 Personal access token (PAT)，访问 [Personal Access Tokens (Classic)](https://github.com/settings/) 来创建一个。

创建 token 时，将「Select scopes」下的「repo」项目全选，其他按需选择。选择完毕后点击最下方的「Generate token」，验证密码后就创建成功了。

{% note warning %}
创建好 PAT 后，token 将只显示一遍。
{% endnote %}

将 GitHub 用户名和刚才创建的 token 配置到 Git 中。
```sh
git config --global github.user username
git config --global github.token usertoken
```

如果使用 Zsh，请将下列代码添加到 `.zprofile` 中：

```sh
github()
{
	username=$(git config github.user)
	[[ -z $username ]] && echo 'GitHub username not get' && exit
	token=$(git config github.token)
	[[ -z $token ]] && echo 'GitHub token not get' && exit
	repo=${1:-$(basename $(pwd))}
	read "newrepo?Repo name (default is $repo): "
	[[ $newrepo ]] && repo=$newrepo
	read "description?Repo description: "
	read "homepage?Repo homepage: "
	read "private?Private repo? (dafault is false): "
	[[ $private ]] && private='true' || private='false'
	curl -u "$username:$token" https://api.github.com/user/repos -d '{"name":"'$repo'","description":"'$description'","homapage":"'$homepage'","private":"'$private'"}'
	git remote add origin git@github.com:$username/$repo.git
	git push -u origin main
}
```

{% note warning %}
在笔者的机器上，默认分支名为 `main`；在有些机器上，默认分支名为 `master`。运行 `git config --list` 查看 `init.defaultbranch` 条目以确定你的默认分支名称，并按需修改上述脚本的倒数第二行。
{% endnote %}

这段代码自动或手动获取仓库基本信息，远程创建仓库并推送到 GitHub 上。里面的 `read` 命令在 Bash 下行为有所不同，如果使用 Bash，请查询 `read -p` 的用法，并自行修改上述代码。重启终端或者运行 `source ~/.zprofile` 来使配置生效。

{% note danger %}
在上传本地仓库前，仓库里至少需要存在一个分支。你可以进行一次提交来自动创建第一个分支。有关提交的说明，请参阅后文。
{% endnote %}

准备好上传后，在仓库根目录运行 `github` 即可在 GitHub 上创建一个公开仓库并将已有的文件推送到远程仓库。远程仓库名默认为本地仓库名，但也可以用 `github repository` 的方式指定远程仓库的名称。

# 使用 Git 管理文件

{% note danger %}
Git 适合管理文本文件，包括但不限于各类程序设计语言的源代码、Jupyter Notebook、LaTeX 源文件等以文本形式存储的文件。尽管你可以将二进制文件放在 Git 仓库中，但 Git 并不能识别出它们之间的差异，因此 Git 仓库可能会变得无比庞大。在 `.gitignore` 中添加这些文件或它们的存储路径以便 Git 忽略这些文件。
{% endnote %}

{% note info %}
你可以在诸如 VSCode 等编辑器中使用集成的 Git 插件来操作 Git 仓库。必要时，也可以使用用命令。
{% endnote %}

## 暂存文件 `git add`

创建一个新的文件后，Git 会将此文件标记为「未跟踪的」（Untracked）。使用 `git add filename` 将此文件添加到「暂存区」，或者 `git add .` 将所有更改（包括未跟踪、已修改和已删除的）文件添加到暂存区。

## 提交文件 `git commit`

当暂存区有文件，希望进行提交时，可以使用 `git commit -m 'message'` 进行提交。提交需要指定提交信息，可以使用 `-m` 开关在同一条命令中指定，否则 Git 将打开一个文本编辑器让你编辑提交信息。

{% note info %}
Git 默认的文本编辑器是 `vi`，而 `vi` 对习惯了图形界面的用户极其不友好。可以通过配置修改 Git 的默认文本编辑器为 `nano`（命令行编辑器，但是比 `vi` 友好太多）或者 `code`（VSCode，现代化的文本编辑器）。
```sh
git config --global core.editor nano
```
或者
```sh
git config --global core.editor code --wait
```
{% endnote %}

如果想要在提交之前暂存所有修改，可以使用 `-a` 开关。

{% note warning %}
注意，`-a` 开关只会暂存「已修改」的文件，不会影响「未追踪」的文件。
{% endnote %}

## 推送更改 `git push`

如果希望将已提交的更改推送到远程仓库，可以使用 `git push` 命令。这将在远程仓库合并本地的修改。如果有冲突，此命令将会出错。如果希望覆盖远程仓库的内容，使用 `git push -f` 强制推送。

## 撤销更改

### 文件已修改，但是未暂存

放弃修改某一文件
```sh
git checkout -- filename
```
放弃修改所有文件
```sh
git checkout -- *
```

### 文件已暂存，但是未提交

```sh
git rm --cached filename
```

### 更改已提交，但是未推送

```sh
git reset --soft HEAD^
git reset --hard HEAD^
```
此时撤销更改将会撤销整个提交。`--soft` 开关将保留提交之前的修改（回到提交之前的状态），而 `--hard` 开关将舍弃提交之前的修改（回到上一次提交的状态）。使用 `HEAD^2` 回退两个或多个提交。

### 更改已推送

在本地执行 `git reset` 命令处理完修改后强制推送 `git push -f`。

## 修改提交

如果你想修改刚刚进行的一次提交，你可以使用 `git reset --soft HEAD^`，进行修改，`git add .`，然后 `git commit`。事实上，还有一种更为简便的方式，那就是在 `git commit` 的同时使用 `--amend` 开关，这会使你的上一次提交的内容替换成这一次提交，因此你需要像进行一个新提交那样指定提交信息，并且 Git 会生成一个新的 commit ID，但是并不会修改提交时间。
```sh
git add .
git commit --amend -m 'message'
```

## 拉取最新内容 `git fetch` `git pull`

`git fetch` 将从远程仓库拉取最新的更改。拉取后还需要执行 `git merge FETCH_HEAD` 使其与本地内容合并。

或者使用 `git pull` 拉取并合并远程内容。

## 分支 `git branch`

Git 允许仓库产生分支，以便同时开发不同的功能。

### 创建分支

使用 `git branch branchname` 创建一个名为 `branchname` 的分支。然后，使用 `git checkout branchname` 切换到另一分支。现在，工作区的所有文件被替换为新分支的内容，所有新的提交也会被提交到新的分支。

### 合并分支 `git merge`

分支开发完毕后，即将合并入主分支或者其他分支。首先用 `git checkout main` 切换到要合并入的分支，然后使用 `git merge branchname` 合并分支。如果有冲突，按照说明解决它们。

{% note warning %}
合并分支并不会删除分支以及分支中的任何内容，只会将另一分支的修改合并至本分支。
{% endnote %}

### 删除分支

分支合并完成后，可以选择执行 `git branch -d branchname` 删除不再需要的分支。如果合并尚未完成，也可以使用 `git branch -D branchname` 强制删除分支。

## 查看仓库状态 `git status`

使用 `git status` 查看仓库状态，包括 `HEAD` 的位置、发生变化的文件、与远程库的比较等等。添加 `-s` 开关以便让 Git 以简短的形式显示更改。

## 查看提交记录 `git log`

使用 `git log` 查看提交记录。添加 `--oneline` 开关将一次提交压缩成一行，添加 `--graph` 开关使用图形的格式呈现记录，添加 `-3` 开关以显示最近三次提交记录。

## 查看具体修改 `git show`

执行 `git show commit-id` 来查看某一次提交的具体修改内容。`commit-id` 可以通过 `git log` 命令查看。

## 创建标签 `git tag`

使用 `git tag` 为当前提交打上一个标签。今后，也可以用标签来代指这一个提交。

{% note info %}
此处的标签（Tag）与 GitHub 的发布（Release）不同。Release 是 GitHub 提供的，它基于 Tag 但又区别于 Tag，且只能在 GitHub 网站上创建。
{% endnote %}

## 修改历史提交信息 `git rebase`

使用 `git rebase -i HEAD~3` 将打开文本编辑器，并显示最近三次提交。将需要修改的提交前面的 `pick` 改为 `edit`，然后保存并退出。

接着，使用 `git commit --amend` 修改提交信息，然后使用 `git rebase --continue` 跳转到下一处修改。中途可以使用 `git rebase --skip` 跳过剩余修改，或者使用 `git rebase --abort` 放弃所有修改。

修改完成后，需要使用 `git push -f` 强制推送。

## 万能后悔药 `git reflog`

Git 记录 `HEAD` 指针的所有操作。即使你执行了 `git reset`，这次重置操作仍然会被 Git 记录。用 `git reflog show` 查看**本地仓库**中 `HEAD` 指针的操作历史记录，并且可以依此还原至某几次操作之前。

---

[^1]: [git - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/Git)
[^2]: [GitHub - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/GitHub)
[^3]: [通过 SSH 连接到 GitHub - GitHub Docs](https://docs.github.com/cn/authentication/connecting-to-github-with-ssh)