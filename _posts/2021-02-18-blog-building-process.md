---
title:  "博客搭建-记录"
categories: 博客搭建
tags: blog jekyll
---

趁着过年这几天，终于把自己的blog搭建好了，第一篇日志简单记录下日志搭建过程。***这目前不是一个小白教程贴，网上教程太多了，这只是一个搭建记录***。

## 为啥要折腾个博客

平时工作和生活中一些想法和经验总想记下来怕以后忘了，以前零零散散的分别在**wiznote**(笔记相关)/**gitkraken boards**(项目和未来的一些计划)/**todoist**(未来的一些计划)上都有一些记录。再加上也一直想有一个自己的博客，完全自主定义内容，格式，啥都能放，还能学一点前端的知识，所以正好就趁着过年这几天折腾折腾。

## 博客框架和主题选择

首先先选择一个托管平台，了解到github pages可以托管静态网站，而且有免费域名xxx.github.io，这样免费域名和托管平台都有了。

目前了解到讨论比较多的博客框架是这三种：hugo/hexo/jekyll。其中的对比，网络上有很多，这里给出一个参考：[ Jekyll / Hugo / Hexo 比较](https://lexcao.github.io/zh/posts/jekyll-hugo-hexo)，看起来jekyll部署起来最快，也什么致命缺陷，github-pages原生支持，所以框架暂时就选jekyll了。

然后就是使用什么主题，刚开始使用的是github官方的[hacker](https://pages-themes.github.io/hacker/)主题，稍微折腾了下，发现不适合做通用blog，各种插件/配置都没，要折腾好得费不少功夫。然后又发现了官方的[minimal](https://pages-themes.github.io/minimal/)主题和另外两个更优秀的非官方主题: [minimal-mistakes](https://mmistakes.github.io/minimal-mistakes/)和[clean-blog-jekyll](https://startbootstrap.com/theme/clean-blog-jekyll)，minimal-mistakes是当前github上star最多的主题了。

目前我的需求很少：

* 方便用markdown写东西
* 能帮我整理标签/分类页面
* 主题清晰简洁

minimal-mistakes都满足，就选它了。

## 主题初步修改

按照[官方文档](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)和网络上的一些指导文档，成功用起来了。

在其默认配置基础上做了一些小修改，幸亏大部分修改主题中都预留了配置接口，在_config.yml中添加或修改就可以了，这也是选择这些用户量大的主题的好处，你的需求很可能被提出甚至被修改过。

主要修改点：

- markdown代码块默认添加行号。
- 文章显示页不显示作者信息，沉浸式阅读，不添加无关组件。
- 文章页显示markdown目录，不随网页滚动，固定在右侧。

其他就是一些小修改点了，还添加了简单的**关于**界面，日后再补充慢慢补充，还有开源项目页日后也会添加进取。

## 结语

接下来就要开始写点有用的了。

感谢开源社区项目贡献，省了很多事，不然又得折腾好久。