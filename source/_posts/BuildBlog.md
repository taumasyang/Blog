---
title: 基于 Hexo 和 Vercel 建立自己的个人博客
date: 2022-09-22 23:32:41
categories: 知识分享
tags:
---

## 博客框架 Hexo

> [Hexo](https://hexo.io) 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

## 静态网页托管平台 Vercel

> [Vercel](https://vercel.com) 是为前端开发人员提供的平台，为创新者提供在灵感迸发时所需的速度和可靠性。
>
> 我们使团队能够快速迭代，开发、预览和交付令人愉悦的用户体验。Vercel 对 35 种以上的前端框架提供零配置支持，并与您选择的无头内容、商务或数据库集成。

### 访问 Vercel

<https://vercel.com>

### 使用 GitHub 登录

### 安装 Vercel-CLI

```sh
npm install -g vercel
```

### 生成静态文件

```sh
hexo g
```

### 部署至 Vercel

```sh
cd public
vercel
```

按提示登录至 Vercel-CLI，创建项目并进行第一次部署。部署完成后，在 Vercel 网页可以查看你的项目以及分配给你的域。

### 二次部署

当你更新了你的博客时，你需要再次部署至 Vercel。`public` 文件夹会在你执行了 `hexo cl` 后被删除，但是里面的 `.vercel` 是你与 Vercel 进行连接的令牌，我们需要保留它。先将它拷贝出 `puclic` 文件夹：
```sh
cp -r .vercel ..
```

再写一个脚本 `deploy.sh`，内容为
```sh
hexo cl && hexo g
cp -r .vercel public
cd public
vercel --prod
```

最后赋予其执行权限：
```sh
chmod +x deploy.sh
```

以后，当你更新了博客，只需要执行这个脚本，就能自动部署你的博客了。

### 绑定域名

如果你自己有域名，你可以将你的博客绑定到你的域名上。进入 Vercel 的项目设置，在 Domains 子目录下添加你自己的域名。添加完成后，还需要到你域名的服务商处修改域名解析。