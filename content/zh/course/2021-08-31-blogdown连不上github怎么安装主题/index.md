---
title: blogdown连不上Github怎么安装主题？
author: 欧阳松
date: '2021-08-31'
slug: blogdown连不上github怎么安装主题
categories:
  - 教程
  - R
  - blodgown
tags:
  - 教程
  - R Markdown
editor_options: 
  markdown: 
    wrap: 72
---

**blogdown**是个好东西，奈何经常连不上Github怎么办，连不上Github，你连第一步都完成不了，还怎么搞个人博客，总不能老去科学上网，这东西多了，你懂的。。。

经过我N次不断的尝试，发现安装主题时（`blog down::install_theme()`），主要是去调取Github的压缩文件（以"archive/master.tar.gz"结尾），我们访问不了Github.com，不代表不能访问镜像啊，所以就有了替代方案，像我这个站，我就用的镜像站点安装的主题。

首先去
<http://themes.gohugo.io/>上挑中了一个主题，比如hugo-universal-theme这个主题，可以知道他的GitHub源码地址是<https://github.com/devcows/hugo-universal-theme>，然而如果直接安装的话可能一直停留在
<https://github.com/devcows/hugo-universal-theme/archive/master.tar.gz>
上无法自拔。

现在国内有很多镜像，比如<https://hub.fastgit.xyz>/ (**目前已不能访问**)
那么我们可以这样把`github.com`替换~~`hub.fastgit.xyz`~~，然后直接输入网址即可安装主题

------------------------------------------------------------------------

你可以先建一个Project，然后直接输入下面的代码就可以直接建站

```{r}
 blogdown::new_site(theme = 'https://hub.fastgit.xyz/devcows/hugo-universal-theme/archive/master.tar.gz')
```

很快文件夹就多了很多东西，预览一下就出来了。。。
