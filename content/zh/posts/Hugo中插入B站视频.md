---
Title: 在Hugo博客中插入B站视频
date: '2021-09-13'
author: 欧阳松
tags: [Hugo, bilibili]
slug: insertbilibili
---
本教程来自于[利用 Hugo Shortcodes 插入 B 站视频](https://mogeko.me/posts/zh-cn/079/)


插入B站视频看最下方：

## 在博客中插入 B 站视频

众所周知，B 站允许使用 `<iframe>` 标签将视频嵌入到别的网站上，我也在很早之前就开始这么玩了。

但这个功能并不好用。第一是被嵌入的视频的大小很难控制，特别是在移动端上。第二就是 Hugo 并不推荐直接在博客中插入 HTML 标签，这样做实际上是有很大的安全风险的。

不过 Hugo 允许自定义 Shortcodes，我们可以利用 Shortcodes 将插入 B 站视频标准化，在保证安全的同时方便用户使用。

## Start

首先是在 `layouts/shortcodes` 文件夹中创建文件 `bilibili.html` (如果没有那个文件夹就自己新建一个)

```html
{{ $videoID := index .Params 0 }}
{{ $pageNum := index .Params 1 | default 1}}

{{ if (findRE "^[bB][vV][0-9a-zA-Z]+$" $videoID) }}
<div><iframe id="biliplayer" src="//player.bilibili.com/player.html?bvid={{ $videoID }}&page={{ $pageNum }}" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" loading="lazy" > </iframe></div>
{{ else }}
<div><iframe id="biliplayer" src="//player.bilibili.com/player.html?aid={{ $videoID }}&page={{ $pageNum }}" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" loading="lazy" > </iframe></div>
{{ end }}
```

其实这样就已经可以用了，不过很难用，因为插的播放器的窗口默认很小，先不说能不能看清视频里的字，光是美观问题都已经能逼得博主们想打人了。

所以我们需要用 CSS 修改播放器的尺寸，最好能根据不同的屏幕大小 (PC 和手机) 动态调整。

所以我们在 `/assets/css/_common/_partials/post.scss` (每个主题的不一样) 中插入

```css
// 嵌入 BiliBili 视频
#bilibili {
  width: 100%;
  height: 550px;
}
@media only screen and (min-device-width: 320px) and (max-device-width: 480px) {
  #bilibili {
    width: 100%;
    height: 250px;
  }
}
```

如果你实在是不知道你的主题的 CSS 文件在哪儿，也可以直接往 `layouts/shortcodes/bilibili.html` 里写

```html
<style>
// 嵌入 BiliBili 视频
#bilibili {
  width: 100%;
  height: 550px;
}
@media only screen and (min-device-width: 320px) and (max-device-width: 480px) {
  #bilibili {
    width: 100%;
    height: 250px;
  }
}
</style>
```

然后就可以用了！

### 用法

用法就是在你想插入 B 站视频的地方写下 (AV号**不**带 `av/AV`，BV号**必须**带`bv/BV`，PV号控制分P，默认为 `1`)

```markdown
{{</* bilibili [AV号/BV号] [PV号] */>}}
```

就比如我想插入 B 站视频：[av2271112](https://www.bilibili.com/video/av2271112)

我只需要

```markdown
{{</* bilibili 2271112 */>}}
```

或者

```markdown
{{</* bilibili BV1es411D7sW */>}}
```

{{< bilibili 2271112 >}}
