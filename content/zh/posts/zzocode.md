---
title: zzo主题的一些短码特效
author: 欧阳松
date: '2021-09-15'
slug: [zzoshortcode]
categories: [zzo]
tags: [zzo]
enableToc: yes
enableTocContent: yes
tocFolding: yes
---

[zzo](https://github.com/zzossig/hugo-theme-zzo)主题是一个很不错的主题，包含了很多实用的功能（另外还有一个相应的简版[zDoc](https://github.com/zzossig/hugo-theme-zdoc)的主题也很不错），而官方的教程网站是：<https://zzo-docs.vercel.app/zzo/>。这里包含了该主题的配置、页码等诸多细节，可控性很强，而一些短码特效（Shortcodes）也相当不错，现在照猫画虎的记录一些短码效果。

## 目录折叠

本文的目录是可以折叠的，这个只需要在`YAML`代码区输入下面两句话即可：

> enableTocContent: yes tocFolding: yes

## 彩色文本框

官方的英文名叫Alert，可以实现彩色文本框的效果。

### 如何使用

目前有四种彩色文本框，分别为黄色、绿色、蓝色和粉色，对应warning, success, info, danger，代码如下：

``` markdown
    {{</*   alert theme="warning" dir="ltr" >}} # warning, success, info, danger
    **this** is a text
    {{< /alert */>}}
```

### 效果展示

使用的时候只要把theme=""里面的单词改一下即可

{{< alert theme="warning" dir="ltr" >}}

**黄色文本框**用waring

{{< /alert >}}

------------------------------------------------------------------------

{{< alert theme="success" dir="ltr" >}}

**绿色文本框**用success

{{< /alert >}}

------------------------------------------------------------------------

{{< alert theme="info" dir="ltr" >}}

**蓝色文本框**用info

{{< /alert >}}

------------------------------------------------------------------------

{{< alert theme="danger" dir="ltr" >}}

**粉色文本框** 用danger

{{< /alert >}}

## 文字添加外框

官方的英文名叫Box，可以实现文字加外框的效果。

### 如何使用

目前有两种文字外框，一种是可以添加markdown语句的复杂外框（Markdownify box），一种是简单语句的外框（Simple box），代码分别是：

``` markdown
{{</* boxmd >}}
Some markdown contents
{{< /boxmd */>}}
```

    {{</* box >}}
    Some contents
    {{< /box */>}}

### 效果展示

使用的时候只要把boxmd或box中间的词语换一下就行

-   可以嵌入markdown语句的外框，用`boxmd`

{{< boxmd >}}
这个*外框*可以嵌入**markdown**语句，比如*斜体*、**加粗**和`列表`等
 - 有序列表
 1. 这个功能不错
 2. 那个功能也可以
 - 无序列表
   - 又是无序
{{< /boxmd >}}

-   简单码字的外框，用`box`

{{< box >}}
这个是简单的外框，不用使用markdown语句，就是简单的码字
{{< /box >}}

## 点击文字展开效果

官方的英文名叫Expand，可以实现点击某段文字展开附加内容的效果，可以使用于问答形式。

### 如何使用

``` markdown
{{</* expand "Expand me" >}}
Some Markdown Contents
{{< /expand */>}}
```

### 效果展示

使用的时候只要把相关词语换一下就行，**可以嵌入markdown语句**

{{< expand "问题：什么是膀胱刺激征?" >}}
回答：尿频、尿急、尿痛同时出现称为膀胱刺激征
常提示膀胱炎症。

---
 - [x] 尿频
 - [x] 尿急
 - [x] 尿痛
{{< /expand >}}

## 插入图片细节

可以使用插头图片的标题、副标题、尺寸和位置等

### 如何使用

``` markdown
{{</* img src="/images/header/background.jpg" title="Sample Image" caption="Image with title, caption, alt, ..." alt="image alt" width="700px" position="center" */>}}
```

### 效果展示

{{< img src="/images/header/background.jpg" title="一个背景图片" caption="这个他的副标题，没有擦干净的黑板" alt="image alt" width="600px" position="center" >}}

## 彩色通知效果

官方的英文名叫Notice，类似于彩色文本框，但是细节更多。

### 如何使用

依旧有四种彩色文本框，分别为绿色、蓝色、黄色和红色，对应success, info,warning和 error，代码如下：

``` markdown
    {{</*  notice success "This is a success type of notice" >}} # # success, info, warning, error
    success text
    {{< /notice */>}}
```

### 效果展示

使用的时候只要notice后面的单词改一下即可

{{< notice success "绿色的提醒" >}} 
今天要开会
{{< /notice >}}

------------------------------------------------------------------------

{{< notice info "蓝色的提醒" >}} 
今天要开会
{{< /notice >}}

------------------------------------------------------------------------

{{< notice warning "黄色的提醒" >}} 
今天要开会
{{< /notice >}}

------------------------------------------------------------------------

{{< notice error "红色的提醒" >}} 
今天要开会
{{< /notice >}}

## 多个选项卡框

官方的英文名叫Tab，可以实现多个选项卡的菜单框效果

```` Markdown
{{</* tabs Windows MacOS Ubuntu >}}
  {{< tab >}}

  ### Windows section

  ```javascript
  console.log('Hello World!');
  ```

  {{< /tab >}}
  {{< tab >}}

  ### MacOS section

  Hello world!
  {{< /tab >}}
  {{< tab >}}

  ### Ubuntu section

  Great!
  {{< /tab >}}
{{< /tabs */>}}
````   

---

### 效果展示

注意选项卡中的内容应彼此不同。

{{< tabs Windows MacOS Ubuntu >}}
  {{< tab >}}

  ### Windows section

  ```javascript
  console.log('Hello World!');
  ```

  {{< /tab >}}
  {{< tab >}}

  ### MacOS section

  Hello world!
  {{< /tab >}}
  {{< tab >}}

  ### Ubuntu section

  Great!
  {{< /tab >}}
{{< /tabs >}}
