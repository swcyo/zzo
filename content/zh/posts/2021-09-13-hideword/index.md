---
title: 防止剧透的隐藏文字
author: Mogeko's Blog
date: '2021-09-13'
slug: hideword
categories:
  - Hugo
tags:
  - Hugo
---
文章出处：
https://mogeko.me/posts/zh-cn/080/

## Start

「隐藏文字」的原理很简单：将文字颜色和背景颜色都设置成黑色，然后再在鼠标移动到文字上时将文字颜色改成白色。

所以我们首先要新建一个 Hugo Shortcodes 来规范化的插入 HTML 标签

在 `layouts/shortcodes` 文件夹中创建文件 `spoiler.html` (如果没有那个文件夹就自己新建一个)

```html
<span class="spoiler" >{{.Inner}}</span>
```

然后再在 `/assets/css/_common/_partials/post.scss` (每个主题的不一样) 中编写 CSS

```css
// 隐藏文字
.spoiler { 
  color: black; 
  background-color:black;
}
.spoiler:hover{
  color: white;
}
```

同样的，如果你实在是不知道你的主题的 CSS 文件在哪儿，也可以直接往 `layouts/shortcodes/spoiler.html` 里写

```html
<style>
// 隐藏文字
.spoiler { 
  color: black; 
  background-color:black;
}
.spoiler:hover{
  color: white;
}
</style>
```

然后就可以用了！
<div id="spoiler"></div>

### 用法

用法就是在你想使用「隐藏文字」的地方这么写

```markdown
{{</* spoiler */>}} 隐藏文字 {{</* /spoiler */>}} 
```

效果：{{< spoiler >}} 猜猜我写的是什么，看到了给你糖糖吃 {{< /spoiler >}}
