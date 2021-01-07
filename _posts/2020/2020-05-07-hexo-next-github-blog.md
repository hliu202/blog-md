---
title: 从 0 开始搭建 Hexo NexT主题的博客
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
# Schemes
# scheme: Muse
scheme: Gemini  # 默认为 Muse 主题 
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

### 修改字体

默认的字体显示 headers 不明显（H1,H2,H3 看上去区别不大），首先是 enable (`themes/next/_config.yml`)

```yaml
  global:
    external: true
    family: Lato
    size: 0.9375   # 默认是 1x16px，改为 15px
```

然后修改大小(`themes/next/source/css/_variables/base.styl`)

```css
// Headings font size
$font-size-headings-step  = .25em //.125em;
$font-size-headings-base  = ... : 2em //1.625em;
```

### 其他

如何修改 `Posted on` （发表时间）? 可以修改 md 中的 `date`

如何隐藏 `Edited on` （修改时间）?

```yaml
  updated_at:
    enable: false
```

如何修改 post/draft 模板？`scaffolds/post.md`

如何插入图片？[设置](https://blog.csdn.net/u010996565/article/details/89196612)

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

## 搜索(Search)

需要安装插件

```
$ npm install hexo-generator-searchdb --save
```

## 数学公式

安装插件

```bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```

`themes/next/_config.yml` 中启用Mathjax

```yaml
  mathjax:
    enable: true
```

在博客中使用

```yaml
---
title: ...
category: ...
mathjax: true
---
```

关于数学公式：[Markdown语法中输入数学公式（MathJax）及特殊符号](https://www.jianshu.com/p/8363e3815b92)

# Last but not least

[官网 getting-started](https://theme-next.js.org/docs/getting-started/) 设置教程非常友好，图文并茂，强烈建议！

比如[Note](https://theme-next.js.org/docs/tag-plugins/note.html):

{% note success %}
#### Success Header
**Welcome** to [Hexo!](https://hexo.io)
{% endnote %}

[Label](https://theme-next.js.org/docs/tag-plugins/label.html)：

> Lorem {% label @ipsum %} {% label primary@dolor sit %} amet, consectetur {% label success@adipiscing elit, %} sed {% label info@do eiusmod %} tempor incididunt ut labore et dolore magna aliqua. Ut enim *{% label warning @ad %}* minim veniam, quis **{% label danger@nostrud %}** exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

其他还有视频，PDF等等

# Ref

[Yaya’s blog](https://yashuning.github.io/tags/hexo/)

[Create Local Search for Your Hexo Blog](https://qiuyiwu.github.io/2019/01/25/Hexo-LocalSearch/)

[hexo 站内文章链接](https://blog.jijian.link/2020-01-08/hexo-anchor-link/)

