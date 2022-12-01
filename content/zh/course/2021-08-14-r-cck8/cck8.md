---
title: R语言处理CCK8或MTT数据，一步绘制生长曲线
author: 欧阳松
date: '2021-08-14'
slug: r-cck8
categories:
  - CCK8
  - 生长曲线
tags:
  - 画图
description: ~
hideToc: yes
enableToc: no
enableTocContent: no
tocFolding: no
tocPosition: inner
tocLevels:
  - h2
  - h3
  - h4
series:
  - ~
image: ~
---

CCK8数据主要是统计OD450的吸收值，一般的数据形式如下，保存为`csv`格式

| Time | NC1         | NC2         | NC3         | OE1         | OE2         | OE3         |
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
| day1 | 0.549070969 | 0.549570976 | 0.547750963 | 0.543670962 | 0.536370963 | 0.545970956 |
| day2 | 0.675742972 | 0.696628983 | 0.690574949 | 0.637390961 | 0.630422963 | 0.653620952 |
| day3 | 0.894142977 | 0.884125994 | 0.882670941 | 0.82057096  | 0.803780962 | 0.819401947 |
| day4 | 1.179670983 | 1.18457301  | 1.178542929 | 1.074040958 | 1.067993961 | 1.052209937 |
| day5 | 1.505662991 | 1.507610027 | 1.500910915 | 1.387150955 | 1.32420196  | 1.389946927 |

## 首先我们要进行数据的预处理,把短数据转换为长数据,然后用ggpubr一步作图


```r
cck8 <- read.csv("~/Desktop/cck8.csv") 
## 宽数据转换为长数据
library(reshape2) 
cck8_long<-melt(cck8,
                 id.vars = c('Time'),
                 variable.name='Sample',
                 value.name='OD450')
## 新增一个分组
cck8_long$Group=rep(c('control', 'treat'), each = 15)  ##分为两组，每组15个数据
### 使用ggpubr画图，可以使用prism主题
library(ggpubr)
ggline(cck8_long, x = "Time", y = "OD450", 
       color = "Group",shape='Group', 
       add = "mean_sd",ylab = "OD450 value")
```

<img src="/zh/course/2021-08-14-r-cck8/cck8_files/figure-html/unnamed-chunk-1-1.png" width="672" />

#### 加个P值，调整坐标，适当美化

-   首先预统计看下结果,记得统计方法一定是'anova',而不是't.test'

```
compare_means(OD450~Group, data=cck8_long, group.by = "Time",method = 'anova')
```


|Time |.y.   |         p|   p.adj|p.format |p.signif |method |
|:----|:-----|---------:|-------:|:--------|:--------|:------|
|day1 |OD450 | 0.0822802| 0.08200|0.08228  |ns       |Anova  |
|day2 |OD450 | 0.0070079| 0.01400|0.00701  |**       |Anova  |
|day3 |OD450 | 0.0003706| 0.00150|0.00037  |***      |Anova  |
|day4 |OD450 | 0.0000675| 0.00034|6.7e-05  |****     |Anova  |
|day5 |OD450 | 0.0030883| 0.00930|0.00309  |**       |Anova  |

-   结果还不错，接着添加显著标志,改色lancet配色


```r
ggline(cck8_long, x = "Time", y = "OD450", 
       color = "Group",shape='Group', title = 'xxx cell',
       add = "mean_sd", palette = 'lancet',ylab = "OD450 value",
       legend = c(0.1,0.9))+
  stat_compare_means(label = "p.signif",
                     aes(group=Group),
                     method = "anova",  
                     hide.ns = TRUE)
```

<img src="/zh/course/2021-08-14-r-cck8/cck8_files/figure-html/unnamed-chunk-3-1.png" width="672" />

-   可以调的细节还有很多,比如换个背景


```r
ggline(cck8_long, x = "Time", y = "OD450", 
       color = "Group",shape='Group', title = 'xxx cell',
       add = "mean_sd", palette = 'lancet',ylab = "OD450 value",legend = c(0.1,0.8),
       ggtheme = theme_bw(base_size = 12))+
  stat_compare_means(label = "p.signif",
                     aes(group=Group),
                     method = "anova",  
                     hide.ns = TRUE)
```

<img src="/zh/course/2021-08-14-r-cck8/cck8_files/figure-html/unnamed-chunk-4-1.png" width="672" />

细节地方AI处理一下,当然如果你安装了`esquisse`，你也可以直接导出成PPT

    esquisse:::ggplot_to_ppt()
