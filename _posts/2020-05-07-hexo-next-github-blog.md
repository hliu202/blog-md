---
title: 从 0 开始搭建 Hexo Next主题的Github博客
date: 2020-05-07 19:03:20
categories: blog
tags: hexo
---

从 0 开始搭建 `Hexo + Next主题 + Github Pages` 博客，主要分为 2 部分

1. 基本配置：基本功能，包括 Hexo 安装，主题安装，配置 yaml，支持 tags 和 categories
2. 扩展功能：包括评论，阅读人数，TODO

<!--more-->

# 基本配置

## Hexo 安装

```bash
sudo npm install hexo-cli -g
hexo init blog
cd blog
hexo new "hello" # 在 source/_posts 生成 hello.md
hexo g # generate 根据 source/ 中的 md 生成 html 页面
hexo s # server 启动 server，通过 localhost:4000 在本地访问网页
```

[Hexo 官网](https://hexo.io/)

### 配置

修改 `blog/_config.yml`:

```yml
# Site
title:
subtitle:
...
# Writing
new_post_name: :year-:month-:day-:title.md # File name of new posts, e.g. 2020-XX-XX-title.md
```

### Github deploy

在 Github 上创建 repo，名叫`<yourname>.github.io`，该 repo 将负责保存和展示你的 blog 页面。

安装 deploy 插件

```bash
npm install hexo-deployer-git --save  # deploy pages to github
```

配置github repo 信息（`blog/_config.yml`）

```yml
deploy:
  type: git
  repo: <repository url> # https://github.com/yourname/yourname.github.io.git
  branch: master
```

deploy 到 github

```bash
hexo d # 将网页 deploy 到 github repo，通过 https://yourname.github.io 来访问
```

## Next 主题

```bash
git clone https://github.com/theme-next/hexo-theme-next.git themes/next
```

使用 next 主题 `blog/_config.yml`:

```yaml
theme: next
```

### 配置

修改 `themes/next/_config.yml`

```yaml
menu:   # 默认只有 home 和 archives：
  home: / || fa fa-home
  tags: /tags/ || fa fa-tags
  categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive
...
social: # 添加 github 链接
  GitHub: https://github.com/yourname || fab fa-github
...
favicon:
  small: /images/avatar.jpg
  medium: /images/avatar.jpg
...
avatar:
  # Replace the default image and set the url here.
  url: /images/avatar.jpg   # 将图片放到 blog/source/images/
```

这样2 个问题

1. menu 里的新增的菜单点击进去，发现是 404，没有相关页面
2. search 点了也不起作用

### tags & categories

添加相关 page

```bash
hexo new page "tags"
hexo new page "categories"
```

修改 `blog/source/tags/index.md`，加上一行

```yaml
type: "tags"
```

修改 `blog/source/categories/index.md`，加上一行

```yaml
type: "categories"
```

### 其他

如何修改 `Posted on` 的发表时间 -> 可以修改 md 中的 `date`

不显示 `Edited on` 修改时间

```yaml
  updated_at:
    enable: false
```

# 扩展配置

涉及 Next 主题的其他配置，比如评论，阅读数等（好多功能摸索中，TODO），需要修改`themes/next/_config.yml`。

## 评论功能(Valine)

```yaml
valine:
  enable: true
  appid:  # Your leancloud application appid
  appkey: # Your leancloud application appkey
```

需要去 [LeanCloud](https://leancloud.cn/dashboard/login.html#/signin) 注册，创建应用，之后，获取 appid 和 appkey，[see more](https://valine.js.org/quickstart.html)

## 阅读数(Views)

```yaml
busuanzi_count:
  enable: true
```

### 搜索(Search)

有 bug 没有解决（deploy 后页面老是显示 Searching…)，所以先把 search 关闭了

# Ref

[Yaya’s blog](https://yashuning.github.io/tags/hexo/)

[Create Local Search for Your Hexo Blog](https://qiuyiwu.github.io/2019/01/25/Hexo-LocalSearch/)