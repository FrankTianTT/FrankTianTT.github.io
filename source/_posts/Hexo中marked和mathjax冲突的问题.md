---
title: Hexo中marked和mathjax冲突的问题
date: 2020-12-10 14:10:36
categories:
  - 技术
tags:
  - 不做程序员
  - 写个博客怎么这么多坑
---

最近打算在博客上整理一些论文笔记，发现hexo默认支持的marked渲染和我附加的mathjax渲染是冲突的，主要有包括两个符号，`\`和`_`。

<!--more-->

网上冲浪了一下找到了三种解决方案，分别是

- 手动在`\`和`_`前面添加转义符，或者在公式前后用保护块，其实用python正则表达式替换一下也不麻烦，但是我是强迫症，放弃。
- 更改marked.js的代码，把marked处理`\`和`_`的部分去掉，留着这两个符号给mathjax处理，但是我是强迫症，放弃。
- 使用pandoc，把原来的marked卸载掉。

显然最后我选择了第三种方法。

主要就是卸载掉原来的渲染引擎`npm uninstall hexo-renderer-marked`，并安装pandoc`npm install hexo-renderer-pandoc`。注意pandoc是需要安装在本地的，在ubuntu下用`apt`安装就好了。在Mac上还有一个坑，我在下载Anaconda的时候它自带了一个版本很久的pandoc，和Hexo使用的pandoc有冲突，需要卸载掉Anaconda自带的，然后手动去官网上安装。

解决问题后我又找到了NexT官方给出的解决方案[数学公式](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/MATH.md)，不过里面有一些东西已经随着NexT本身的更新消失了。

总之就是非常烦😡。
