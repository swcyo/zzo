---
title: 使用CBCgrps绘制临床基线特征表，可用于两组和多组比较
author: 欧阳松
date: '2022-12-01'
slug: CBCgrps
categories:
  - CBCgrps
  - 临床基线
tags:
  - 教程
description: ~
hideToc: no
enableToc: yes
enableTocContent: no
tocFolding: no
tocPosition: inner
tocLevels:
  - h1
  - h2
  - h3
series:
  - ~
image: ~
---

<script src="/rmarkdown-libs/htmlwidgets/htmlwidgets.js"></script>
<link href="/rmarkdown-libs/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="/rmarkdown-libs/datatables-binding/datatables.js"></script>
<script src="/rmarkdown-libs/jquery/jquery-3.6.0.min.js"></script>
<link href="/rmarkdown-libs/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="/rmarkdown-libs/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="/rmarkdown-libs/dt-core/js/jquery.dataTables.min.js"></script>
<link href="/rmarkdown-libs/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="/rmarkdown-libs/crosstalk/js/crosstalk.min.js"></script>

我们处理TCGA或GEO的临床数据时，有时候需要进行分组的比较，目前可以使用tableone、compareGroups等实现，本文推荐另外一个R包：**CBCgrps**，是中国浙江省邵逸夫医院急诊科的章仲恒主任编写。

当然也有过中文教程，[CBCgrps中文版教程 (zh-zhang1984.github.io)](https://zh-zhang1984.github.io/CBCgrps%E6%95%99%E7%A8%8B.html#2_cbcgrps%E7%AE%80%E4%BB%8B)

## 1 如何引用

如果觉得下面的教程对您的科研工作有用，请不要感谢我，也不要点赞，只要帮忙引用下面两篇文献就是最大的满足了：

> Zhang Z. Univariate description and bivariate statistical inference: the first step delving into data. Ann Transl Med. 2016;4(5):91. <doi:10.21037/atm.2016.02.11>

> Zhang Z, Gayle AA, Wang J, Zhang H, Cardinal-Fernández P. Comparing baseline characteristics between groups: an introduction to the CBCgrps package. Ann Transl Med. 2017;5(24):484. <doi:10.21037/atm.2017.09.39>

## 2 CBCgrps简介

在大数据时代，研究者能够获取越来越多的医疗数据进行临床研究，比如电子病历数据、新冠肺炎的实时呈报数据。这些数据本质上都属于回顾性观察性研究，因此就必须符合观察性研究的各种标准（STROBE），笔者进行了大量的类似研究，每次研究发现都会进行大量的单变量分析，即我们平时常说的t检验、卡方检验等，这些检验做完后一般采用表格形式进行报告。如果只进行10个以内变量的表格呈报，一般手工操作尚能胜任，但如果变量多达几十个，那么此时手工填报就显得费时费力，且容易出错，而且不利于后期的修改维护。

计算机是懒人的的福音，能将大量重复劳动简化，*CBCgrps*就是为了解决制作基线表格费时费力的缺点，能快速准确制做出基本符合发表格式的基线表格。另外类似的程辑包还有*tableone*，读者可以自行比较各自的优缺点。

废话不说进入正题。

## 3 CBCgrps快速上手

### 3.1 安装程辑包

这个步骤很简单，只需要下面的两句即可

``` r
# install.packages('CBCgrps') ## 安装方法
library(CBCgrps)
```

如果安装不成功可能要设置镜像，或者是手动从CRAN官网下载后手动安装。

### 3.2 进行简单的两组间比较

    data(dt)
    head(dt)  #粗略查看一下数据长什么样

``` r
data(dt)
```

<div id="htmlwidget-1" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"filter":"none","vertical":false,"data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51","52","53","54","55","56","57","58","59","60","61","62","63","64","65","66","67","68","69","70","71","72","73","74","75","76","77","78","79","80","81","82","83","84","85","86","87","88","89","90","91","92","93","94","95","96","97","98","99","100","101","102","103","104","105","106","107","108","109","110","111","112","113","114","115","116","117","118","119","120","121","122","123","124","125","126","127","128","129","130","131","132","133","134","135","136","137","138","139","140","141","142","143","144","145","146","147","148","149","150","151","152","153","154","155","156","157","158","159","160","161","162","163","164","165","166","167","168","169","170","171","172","173","174","175","176","177","178","179","180","181","182","183","184","185","186","187","188","189","190","191","192","193","194","195","196","197","198","199","200","201","202","203","204","205","206","207","208","209","210","211","212","213","214","215","216","217","218","219","220","221","222","223","224","225","226","227","228","229","230","231","232","233","234","235","236","237","238","239","240","241","242","243","244","245","246","247","248","249","250","251","252","253","254","255","256","257","258","259","260","261","262","263","264","265","266","267","268","269","270","271","272","273","274","275","276","277","278","279","280","281","282","283","284","285","286","287","288","289","290","291","292","293","294","295","296","297","298","299","300","301","302","303","304","305","306","307","308","309","310","311","312","313","314","315","316","317","318","319","320","321","322","323","324","325","326","327","328","329","330","331","332","333","334","335","336","337","338","339","340","341","342","343","344","345","346","347","348","349","350","351","352","353","354","355","356","357","358","359","360","361","362","363","364","365","366","367","368","369","370","371","372","373","374","375","376","377","378","379","380","381","382","383","384","385","386","387","388","389","390","391","392","393","394","395","396","397","398","399","400","401","402","403","404","405","406","407","408","409","410","411","412","413","414","415","416","417","418","419","420","421","422","423","424","425","426","427","428","429","430","431","432","433","434","435","436","437","438","439","440","441","442","443","444","445","446","447","448","449","450","451","452","453","454","455","456","457","458","459","460","461","462","463","464","465","466","467","468","469","470","471","472","473","474","475","476","477","478","479","480","481","482","483","484","485","486","487","488","489","490","491","492","493","494","495","496","497","498","499","500"],[62,67,93,71,72,96,77,51,60,63,88,75,76,72,62,97,77,41,81,63,54,67,55,59,61,45,83,72,53,89,76,66,83,83,82,80,78,69,65,64,60,67,51,103,88,53,64,63,82,69,74,70,69,91,67,93,47,79,72,73,76,62,65,55,54,75,77,71,84,101,63,35,85,59,60,85,66,52,73,68,70,76,64,80,67,75,86,77,65,87,85,78,74,61,90,61,103,93,66,55,59,74,66,65,56,69,58,45,64,84,61,79,46,69,78,75,72,60,57,55,72,56,63,66,98,60,74,71,56,69,92,77,71,64,39,87,48,81,99,48,81,66,46,47,46,62,48,80,102,51,82,82,75,55,68,66,78,64,85,64,86,54,51,119,64,74,80,63,78,76,67,71,69,102,59,54,71,75,77,63,54,89,65,57,66,67,87,71,81,63,73,65,71,57,50,100,79,51,61,52,103,90,66,78,64,63,58,61,95,69,72,74,88,62,55,95,63,59,51,51,61,79,87,81,65,71,59,59,83,55,99,69,73,59,61,50,67,76,75,58,58,62,92,53,67,99,68,50,60,77,64,62,65,71,94,69,86,79,68,47,62,63,71,90,104,93,68,44,64,71,83,84,80,49,83,63,73,71,76,70,45,81,76,66,72,72,73,95,67,73,88,86,87,61,100,71,98,50,70,89,59,59,56,54,63,75,40,73,89,101,90,81,44,61,65,81,68,51,95,84,74,88,50,80,62,80,69,79,90,70,85,52,59,93,76,39,50,67,83,68,79,84,95,71,69,44,71,61,55,67,85,40,64,72,57,75,76,70,33,109,67,80,74,85,82,67,76,56,83,63,106,45,63,82,78,61,55,72,70,43,71,73,73,54,77,91,77,53,63,75,60,38,83,58,61,93,58,83,51,65,69,52,60,70,80,45,65,81,62,73,77,74,80,68,64,30,69,76,78,62,97,74,72,89,59,63,106,70,95,48,67,76,75,55,70,54,81,86,37,89,51,77,80,67,60,72,77,83,39,45,91,86,77,81,84,30,87,63,73,66,83,65,78,64,54,88,81,96,71,87,100,66,50,66,67,72,96,65,76,67,70,75,90,72,81,82,84,61,94,64,68,91,89,54,57,50,73,72,75,78],["male","male","male","female","female","male","female","male","male","male","male","male","male","male","male","female","male","female","male","male","male","female","male","male","female","male","male","male","male","male","male","male","male","male","female","male","male","male","male","male","female","female","male","male","male","male","male","male","female","female","female","female","female","male","male","male","female","male","male","female","female","male","male","male","male","female","male","male","female","male","female","female","female","female","female","female","male","male","male","male","male","male","male","male","female","male","male","male","female","male","female","male","male","male","female","female","female","female","male","male","female","female","male","female","female","male","male","female","female","female","male","female","male","male","male","male","male","female","male","male","male","male","female","male","male","male","female","male","male","male","male","female","female","male","female","female","male","male","male","male","female","male","male","female","male","male","female","female","male","male","female","male","female","female","male","male","male","female","male","male","male","male","male","male","male","male","female","male","male","female","male","male","female","female","male","male","female","male","male","female","male","male","female","female","female","male","male","female","female","male","male","male","male","female","male","male","female","male","female","male","female","female","male","female","male","female","male","male","female","male","male","female","male","male","male","female","male","female","male","female","male","male","male","female","female","male","male","male","female","male","male","male","male","male","male","male","male","female","female","male","male","male","female","male","female","male","male","male","male","female","female","male","male","male","male","female","male","male","female","male","male","female","male","male","female","male","female","female","male","female","male","female","male","male","female","male","female","female","male","male","male","female","female","female","male","male","female","male","male","male","female","male","male","female","male","male","female","female","female","male","male","male","male","female","female","male","male","male","female","male","male","male","female","male","female","male","female","male","male","male","female","male","female","male","female","male","male","male","male","female","male","male","male","female","male","male","female","female","female","male","male","male","male","male","female","female","male","female","male","male","male","male","male","male","male","male","male","female","male","male","female","male","female","male","male","male","male","male","male","male","male","male","male","female","female","female","male","female","male","female","male","male","female","male","female","male","male","female","male","male","female","female","male","male","male","female","female","male","male","male","male","male","male","male","male","female","male","female","male","female","female","female","female","male","male","female","male","male","female","male","female","male","male","female","male","male","male","female","female","male","female","male","female","female","male","male","female","female","female","male","male","female","male","male","female","female","male","male","male","male","male","female","female","male","male","female","male","female","female","male","female","female","female","male","male","female","female","male","male","male","female","male","female","male","male","female","male","female","female","female","female","male","female","female","female","male","female","male","male","female","male","male","female","female","male","female","female","male","female","male"],[7.6,4.3,5.5,4.9,4.1,4.3,6.5,4.1,null,4.1,5.6,5.7,5.7,null,5.2,6.4,7.1,4.1,3.9,7.5,null,3.6,5.4,1.3,5.1,8.3,4.5,3.9,5.4,3.9,6.1,6.3,4.5,2.1,null,3.3,6.1,0.6,0.7,3.2,5.3,null,6.3,5.2,4.2,6.1,5.3,3.6,5,5.8,5.2,3.2,6.2,6.8,5.1,4.8,4.3,8.8,5.1,4.2,0.5,null,4.3,4.9,5.1,6.1,4.1,6.9,null,2.1,null,6,4.3,6.1,4,3.3,3.8,0.8,2.2,1.6,6.6,3.3,6.1,7.5,4.1,5.1,null,2.9,4.6,5.1,3,9.9,3.6,4.6,5.8,4.4,3.2,6.6,7.7,4.4,5.6,4.3,6.5,2.2,9.1,3.3,1.6,3.8,4.8,7.7,6.3,4.8,null,2.8,4.2,9.6,0.8,6.8,3.4,7.8,2.2,4.8,2.3,6.3,7.5,8.4,6.1,8.2,7,4.2,5.5,2.6,4.1,6.9,5.6,5.4,null,3.3,3,5.3,2.5,5.6,null,8.2,5.4,3.1,5,3.8,5.2,5,null,2.9,5.1,7.8,6.7,3.3,6.2,4.4,5.1,5,9.7,null,4.7,null,5.7,6.7,7.4,0.6,5.3,7.7,3.7,4.1,4.4,5.2,5.8,7.1,4.3,4.9,9.5,5.4,4.9,1.8,4.5,4.1,4.5,3.3,3.1,3.1,4.1,7.2,6.6,3.8,1.1,null,3.6,4.7,3.2,8.5,4,null,4.2,2.5,4.8,5,5.2,1.3,4.9,5.1,1.7,6.4,6.1,9.1,4.8,4.6,4.7,4.6,0.8,null,4.3,3.3,4.6,0.3,1.5,null,6.5,2.3,2.9,4.7,3.9,4.8,9.1,2.3,3.9,3.5,7.5,null,4.3,6.1,2.4,7.8,5.9,2.4,5.4,4.1,5.1,4.3,6.6,1.8,3.5,4,null,null,4.5,4.2,0.6,6.6,5,9.3,5.9,3.6,10.1,10.2,2.1,5.4,4.1,4.9,5,2,5.1,8,4.2,4.2,7.3,6.3,1.2,5,7.8,7.3,5.3,5.9,2.1,5.1,2.6,3.6,6.4,2.2,5,5.4,4.6,8.1,null,5.7,4.3,4,5.4,2.5,1.9,3.5,8,4.6,5.2,4.1,5.4,2.6,6.8,5.7,2.9,4.6,5.9,5.6,7.5,3.3,6.7,6.6,4.2,2.5,2.3,5.4,4.4,8.1,5.6,3.8,2.4,3.3,5.2,3.6,5.5,3.8,4.3,3,8.3,5.2,5,1.1,5.8,2.5,2.7,6.3,3.6,1,8,null,5.6,6.5,3.2,5.4,5.4,6.6,5.5,4,6.3,6.1,2.6,null,4.8,4,null,4.7,null,4.8,3.3,6.4,9.3,null,6.6,6.2,4.4,3.6,7.7,0.5,3.2,1.6,7.3,4.1,3.5,10.9,4.4,3.6,5.1,1.4,5.5,4.3,5.4,2.5,2.5,3.1,null,7.1,7.7,4.2,3.8,3.8,4.6,4.7,5.1,3,2.8,6.9,3.4,5.1,4.4,2.6,5.7,4.8,7.3,4.7,3.8,5.1,2.1,2.8,5.5,3.8,4.4,4.3,3.2,null,5.2,6,1.9,7.5,5.3,7.2,2.6,4.8,4.3,6,3.3,4.5,null,2.9,4.8,3.1,4.4,3.6,5.7,6.8,null,3.9,7.4,0.1,3.9,8.6,8.9,4.8,2.8,4.8,3.2,7.4,2.9,5.2,5.7,3,6.2,null,5.8,6.7,2.5,6.7,3.5,5.1,4.9,3.6,5,null,4.2,6.2,0.2,1.5,2.2,1.3,5.3,2.5,0.2,3.2,3.7,6.2,null,6.8,5.3,5.2,2.8,8.4,4.2,0.9,4.1,null,3.9,3.7,5.9,6.5,null,6.1,7.3,2.9,3.2,5.8,4.9,5.6,7.3,5.3],["surgery","medical","surgery","emergency","medical","medical","medical","surgery","surgery","medical","emergency","emergency","emergency","emergency","surgery","medical","medical","medical","medical","emergency","surgery","medical","surgery","emergency","emergency","surgery","emergency","emergency","medical","surgery","surgery","medical","medical","medical","surgery","emergency","medical","surgery","surgery","surgery","surgery","emergency","surgery","surgery","medical","surgery","emergency","surgery","medical","emergency","medical","emergency","medical","medical","surgery","medical","medical","emergency","medical","emergency","emergency","surgery","medical","medical","medical","surgery","emergency","emergency","emergency","surgery","medical","medical","emergency","medical","emergency","emergency","emergency","emergency","emergency","surgery","surgery","emergency","surgery","emergency","medical","medical","surgery","surgery","medical","emergency","medical","surgery","medical","emergency","emergency","surgery","medical","surgery","surgery","emergency","emergency","medical","emergency","surgery","emergency","emergency","medical","emergency","medical","medical","surgery","emergency","emergency","medical","medical","surgery","emergency","medical","medical","surgery","surgery","emergency","emergency","medical","surgery","medical","emergency","emergency","emergency","emergency","medical","surgery","emergency","medical","emergency","emergency","surgery","medical","medical","medical","emergency","surgery","emergency","emergency","surgery","surgery","surgery","emergency","medical","emergency","surgery","surgery","surgery","emergency","emergency","medical","emergency","surgery","emergency","medical","emergency","emergency","surgery","emergency","emergency","medical","medical","medical","medical","emergency","emergency","emergency","surgery","emergency","emergency","emergency","medical","surgery","emergency","medical","medical","emergency","surgery","surgery","emergency","surgery","emergency","surgery","emergency","surgery","emergency","emergency","emergency","medical","medical","surgery","emergency","surgery","medical","surgery","medical","surgery","emergency","medical","emergency","surgery","surgery","surgery","surgery","medical","surgery","medical","medical","emergency","emergency","surgery","emergency","emergency","medical","medical","surgery","emergency","medical","surgery","surgery","surgery","surgery","emergency","emergency","emergency","medical","surgery","surgery","medical","surgery","medical","medical","surgery","medical","surgery","emergency","emergency","emergency","emergency","surgery","surgery","emergency","emergency","emergency","medical","surgery","emergency","emergency","medical","emergency","medical","emergency","surgery","surgery","medical","medical","emergency","medical","emergency","medical","medical","surgery","emergency","surgery","medical","emergency","emergency","medical","medical","medical","surgery","surgery","emergency","emergency","emergency","emergency","surgery","emergency","surgery","surgery","medical","surgery","emergency","surgery","surgery","emergency","medical","medical","medical","surgery","medical","medical","medical","emergency","surgery","emergency","medical","emergency","emergency","surgery","medical","emergency","surgery","medical","surgery","surgery","emergency","surgery","surgery","surgery","emergency","emergency","surgery","emergency","surgery","medical","emergency","surgery","surgery","emergency","emergency","surgery","emergency","medical","emergency","medical","medical","medical","surgery","medical","emergency","surgery","medical","surgery","surgery","medical","medical","surgery","surgery","medical","surgery","surgery","emergency","emergency","emergency","surgery","emergency","medical","emergency","surgery","medical","medical","surgery","emergency","surgery","surgery","emergency","emergency","emergency","medical","surgery","medical","surgery","emergency","emergency","medical","emergency","emergency","emergency","surgery","surgery","emergency","emergency","emergency","surgery","medical","medical","surgery","medical","emergency","surgery","surgery","emergency","medical","surgery","emergency","surgery","emergency","emergency","emergency","emergency","surgery","medical","medical","emergency","surgery","medical","surgery","emergency","surgery","medical","surgery","emergency","emergency","surgery","medical","medical","surgery","emergency","surgery","medical","surgery","surgery","medical","surgery","emergency","medical","surgery","emergency","medical","emergency","surgery","surgery","medical","medical","medical","emergency","medical","emergency","surgery","emergency","medical","emergency","emergency","medical","emergency","surgery","emergency","emergency","emergency","surgery","medical","medical","emergency","medical","emergency","medical","surgery","emergency","emergency","surgery","emergency","surgery","medical","emergency","surgery","emergency","surgery","medical","medical","emergency","surgery","emergency","emergency","emergency","surgery","emergency","emergency","emergency","medical","medical","surgery","emergency","surgery","emergency","surgery","emergency","emergency","emergency","emergency","surgery","surgery","emergency","emergency","medical","medical","emergency","surgery","emergency","surgery","emergency","surgery","emergency","medical","medical"],["No","Yes","No","No","No","No","Yes","No","Yes","Yes","Yes","No","No","No","No","No","Yes","No","Yes","No","No","Yes","No","No","Yes","Yes","Yes","Yes","No","Yes","Yes","No","No","No","Yes","No","No","No","No","Yes","No","No","No","No","No","Yes","No","No","No","Yes","No","No","Yes","No","No","Yes","No","Yes","Yes","Yes","Yes","No","No","No","No","Yes","No","Yes","No","No","Yes","Yes","No","No","No","Yes","No","No","Yes","No","Yes","No","No","No","No","No","Yes","No","No","No","Yes","Yes","No","No","Yes","No","No","No","No","No","No","Yes","No","Yes","No","No","Yes","No","Yes","No","No","No","No","No","No","Yes","No","Yes","Yes","No","No","No","Yes","Yes","No","Yes","Yes","No","No","Yes","No","No","Yes","No","No","No","No","No","No","No","Yes","No","No","No","No","No","No","Yes","No","No","Yes","Yes","No","No","No","No","No","No","No","No","No","No","Yes","No","No","Yes","No","No","No","No","No","No","No","Yes","No","No","Yes","No","Yes","Yes","No","Yes","No","No","No","No","No","No","No","Yes","No","No","No","Yes","Yes","Yes","No","No","Yes","No","No","No","Yes","No","No","No","No","No","No","No","No","No","Yes","No","Yes","No","Yes","No","Yes","No","Yes","No","Yes","No","No","Yes","No","No","No","No","No","No","No","No","Yes","No","Yes","No","No","No","No","No","Yes","Yes","Yes","Yes","Yes","No","No","Yes","No","No","No","No","Yes","No","No","Yes","Yes","No","No","No","No","No","No","Yes","Yes","No","No","No","No","No","Yes","No","Yes","Yes","No","No","No","No","No","No","No","No","Yes","No","Yes","No","Yes","No","No","No","No","No","No","No","Yes","No","No","Yes","No","No","No","No","Yes","No","Yes","No","No","No","No","Yes","No","Yes","No","No","Yes","No","No","No","Yes","No","No","No","Yes","No","No","Yes","No","No","No","No","Yes","No","Yes","No","No","No","Yes","No","No","Yes","Yes","No","No","Yes","No","Yes","Yes","No","Yes","No","Yes","No","No","No","No","No","Yes","No","No","No","Yes","Yes","No","No","Yes","No","No","No","Yes","No","No","Yes","No","Yes","No","Yes","No","No","Yes","No","No","No","No","No","Yes","No","No","No","No","No","No","No","No","Yes","No","No","No","No","No","No","No","No","No","No","Yes","No","Yes","No","No","No","No","Yes","Yes","No","Yes","No","Yes","No","No","No","Yes","No","No","No","Yes","No","No","Yes","No","No","No","No","No","No","Yes","Yes","No","No","No","Yes","No","No","No","No","No","No","No","No","No","No","No","No","No","No","No","No","No","No","Yes","No","No","No","No","Yes","Yes","No","No","No","No","No","Yes","Yes","No","No","No","No","No","Yes","No","No","No","No","No","No","Yes","Yes","Yes","Yes","No","No","No","Yes","No","Yes","Yes","No","No","No"],[6.5,null,9.3,4.4,12.3,17.6,null,11,6.7,12.8,null,1.7,null,9.8,null,3.9,10.9,14,15.3,9.9,6.3,9.7,19.7,12.4,0.2,17.1,12.7,null,9.9,9.3,11.3,7.5,null,5.3,11,null,12.5,null,4.5,10.3,13.1,null,17.8,null,10.8,10.8,10.6,8.9,12.2,null,3.6,6.7,null,null,17.9,11.6,11.9,8.8,10.2,3,11.4,7.3,11.7,8.3,6.7,8.9,8.2,15.5,11,12.2,null,17.5,null,null,null,14.4,3.1,18.5,9.2,15.6,10.4,8.3,0.8,4.5,9.8,14.1,null,16.9,17,4.3,null,4.8,9.4,14.6,14.3,5.8,8.1,5.1,11.7,2.2,3.1,17.3,null,7.7,null,null,17.4,null,11,1,15.4,8.2,null,9,9.4,15.3,4.2,null,null,null,15.8,null,11,7.1,12.8,15.3,22.1,0.2,11.4,13.3,9.7,2.2,13.7,6.1,12.5,null,16.4,null,15.4,6.2,3.6,16.6,15.7,8.8,13.8,5.7,9.2,null,8.6,null,17.7,null,null,18.2,8.4,3,12.6,null,5,null,22.2,4.8,15.2,9.5,10.7,16.1,null,5.7,null,13.4,1.7,15.5,2.2,10.1,9,16.5,10.9,10.5,13.5,5.9,9,15.9,13.4,3.6,13.2,3.1,7,16.1,1.3,1.1,16.5,5.9,16.2,9.2,12.1,14.1,null,8.5,7.9,null,10.6,null,null,8.8,16.9,null,10.1,null,7.8,19.8,2.9,20.1,8.2,null,0,null,14.4,3.6,6.2,11,15.7,6.2,8.9,null,4.4,14.7,12.2,null,6.7,13.9,null,17.1,7,1.4,15.2,null,null,18.6,8.7,17.7,4.5,14.6,11.2,6.3,3.6,10.4,11.3,11.4,12.7,7.7,7.4,11.2,7.3,16.1,10.9,6.9,1,6.8,null,7.2,5.8,13.3,12.2,13.8,8.6,15.6,4.1,9.8,6.5,13.4,null,null,20.3,10.7,7.3,5.9,8.7,9.7,20,null,4.5,null,12.9,null,null,10.6,9.8,16,15.4,8.4,10.2,13.3,1.7,8.4,12.9,7.4,9.7,9.9,6.4,null,9,8.3,null,null,9.9,12,9.6,13,11.1,null,15.6,5.1,17.2,11.7,10.9,7.2,9.2,11.9,0.3,7,null,8.5,6,6.8,11.2,1.8,14.4,0.8,7.5,6.1,3.8,null,11.3,9.4,8.5,1.6,3.4,9.3,5.6,6,null,10.9,4.8,7.7,8.6,6.6,5.2,9.7,13.7,11.1,9,11.9,14,9.3,12.3,4.3,8.8,7.9,11,11.8,null,6.2,null,11.4,16.8,6.1,8.1,null,7.2,10.6,9.8,null,14.2,14.2,15.1,16.3,7.5,null,11.3,13.2,8.3,7.9,6.9,17.4,2.5,9.2,10.2,null,11.9,0.2,14.8,1.7,1,6.2,null,null,7.2,11.7,4.7,9.6,null,18.2,null,18.6,12.3,10,1.3,3.9,null,13.6,8.4,12.7,null,8.6,null,null,10.6,null,11.9,null,9.8,0.3,null,2.6,2,7.5,null,11.4,15.5,null,null,null,8.5,15.4,10,1.6,5.8,15,6.9,11.7,10.1,7.5,8.2,12,11,16.2,null,5.4,10.8,13.5,21.4,7.7,null,14.4,16.4,6.7,13.3,7.2,null,13.1,10.5,11.9,7.1,19,13.3,8.1,13.5,3.9,9.3,17,9.1,14.6,8.9,1.5,5.6,11.1,null,null,11.4,5.2,4.9,null,16.8,14.7,13,15.6,null,null,11,4.8,5.8,10.3,11,8.4,5,0.5,12.1,8.5,null,2.3],[null,141.1,37,null,212.7,222.1,67.6,128.2,null,175.3,147.6,null,88.1,null,184.9,195.7,180.1,82.6,7.1,null,135.8,null,232.6,78.5,241.5,116.9,92.9,133.5,181.2,133.4,null,127.5,53.4,109.3,null,136.5,205.1,188.9,201.7,null,10.9,150.3,280.4,111.6,186.2,null,null,128.2,98.1,155.8,166.6,null,152.5,182.9,87.9,241.2,326.1,268.2,null,4.2,null,220.4,212.2,51.6,null,160.7,200.1,219.8,null,72.6,45,11,190.4,216.2,176.9,65.3,275.4,120.4,292.3,194.1,245.2,176.4,null,null,105.3,146.2,172.6,147.3,135.8,164.7,108.1,107.1,null,217.7,153.8,143.3,null,null,63.1,66.1,null,228.5,160.1,null,243.1,200,209.4,131.9,116.2,142.2,182.4,214,277.2,109.3,68.8,158.1,391.8,115.7,241.7,130.6,111.7,93.2,133.1,281.3,174.1,189,217.1,null,212,35.8,null,22.8,77.9,null,110.5,181.6,244.5,109.1,139.2,178,168.1,88.1,208.5,193.7,126.9,null,null,264.2,null,219.8,111.3,107.5,103,117.1,206.7,170.5,1.5,1.2,null,162,34.6,84.7,152.2,null,null,49.6,63.6,164,176.5,124.3,21,99.6,105.2,133.8,null,95.9,null,93.6,50.8,200.9,65.9,368.8,157.4,null,144.7,297.5,203.1,129.9,null,null,87.2,247.2,134.9,null,213.4,257.6,94.4,null,null,149.6,182.5,287.1,145.2,null,188.8,146.1,200.2,132.1,null,24.9,null,108.7,54.8,117,179.8,157.4,205.5,225.3,216.3,null,43.7,127.6,null,140.3,19,189.3,172.5,118,163.9,259.6,253.9,113.5,null,33.7,91.8,310.7,269.8,131.7,94.6,40.7,320.1,159.2,102.5,236.2,62,208.1,null,133.2,266.1,201.3,95.7,null,null,107.3,212,null,148,231.5,53.6,null,null,199.2,183.8,110.3,189.3,109.8,null,95.1,213.1,205.3,249.4,308.6,98.3,227.3,null,null,225.6,90.9,177.7,78,120.4,146.7,101,5.7,169.4,null,null,110.9,222,236.2,39.9,240,null,122.4,61.3,99.6,255.4,192.8,189.5,16.2,null,200.3,218.9,72.1,null,38.4,289.1,86.1,211.2,175.4,64.9,null,152.7,null,167.5,null,151.7,null,null,192.2,180.3,217.5,100.3,164.2,null,222.7,99.1,288.6,null,148.9,null,143.2,195.1,null,null,null,49.9,null,113.5,204.6,130.9,null,322.5,null,null,null,null,null,34.7,209.2,null,76.4,53.8,null,28.7,null,null,12.8,178.4,null,47.5,146.7,103,79,169.1,218.1,288.2,60.6,null,73.8,242.5,177.7,265.6,31.4,189.6,173.6,233.8,131.5,241.5,114.4,115.7,null,64.5,113.5,null,282.1,239.1,116,175.5,null,49.3,170.6,null,null,208,93.4,138.5,72.1,null,189.4,190.2,64,250.6,269.4,179.8,162.6,156.2,170.6,180.1,160.9,null,123.1,240.3,147,9.5,142,185.8,248.4,154.8,5.2,null,54.1,71.9,null,86.1,null,83.5,114.9,164.7,221.2,96.7,83.9,108.5,56.3,null,24.6,null,34.7,6.5,152.3,273.1,null,105,94.2,null,206.8,54.9,null,null,90.5,10.5,null,18.1,148.8,78.4,153.7,186.9,null,68.2,101.1,96.6,null,87.6,134.5,132.3,null,253.4,null,null,98.2,null,197.5,null,182.2,51,null,106.8,131.1,95.6,null,null,229.3,204,155.9,208.9,202.9,278.3,218,null,132.8,112.6,120.9,null,247.8,36.3,93.9,139.4,47.3,279.5,null],[1,1,1,1,1,1,1,0,0,1,1,1,1,0,0,1,1,0,1,1,0,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,0,0,1,0,1,1,1,1,1,1,0,1,1,1,1,1,1,0,1,1,1,1,1,1,0,1,0,0,1,1,1,1,1,1,0,1,1,1,1,1,0,1,0,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,0,0,1,1,0,1,1,0,0,1,1,1,1,1,1,1,1,1,1,1,1,0,0,1,1,1,1,1,1,1,1,1,0,1,1,0,1,0,1,1,1,1,0,0,1,0,0,0,1,1,1,0,1,1,1,1,1,1,0,1,1,1,0,0,1,0,1,1,0,1,1,0,1,0,1,1,0,1,1,1,1,0,1,1,0,1,1,1,0,1,1,1,1,0,0,1,1,1,1,1,0,1,1,1,1,1,0,1,0,1,1,1,1,1,0,1,1,1,1,1,0,0,0,1,0,0,1,0,1,1,1,1,0,1,0,1,1,1,1,1,0,1,0,1,1,1,1,1,0,1,1,0,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,0,1,1,1,0,1,1,1,1,1,1,0,1,1,1,1,1,1,1,0,1,1,1,1,1,1,1,1,1,1,1,1,0,0,1,1,0,1,1,1,1,1,1,1,1,1,1,1,0,0,1,0,1,1,0,0,1,0,1,1,1,1,1,1,0,1,0,1,0,1,1,1,1,1,1,1,0,1,1,1,0,1,1,1,1,1,1,0,0,1,0,1,1,0,1,0,1,1,0,1,1,1,1,1,1,1,0,1,1,1,1,0,0,1,1,1,0,1,1,0,1,1,1,1,0,1,1,1,1,1,0,1,0,1,1,0,1,1,1,1,1,1,1,1,1,0,0,1,1,1,1,1,0,1,1,0,1,1,0,0,0,1,1,1,1,1,0,1,0,0,1,1,1,1,1,1,1,1,1,1,0,1,1,1,1,1,1,0,1,1,1,0,1,1,1,1,1]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>age<\/th>\n      <th>gender<\/th>\n      <th>lac<\/th>\n      <th>type<\/th>\n      <th>vaso<\/th>\n      <th>wbc<\/th>\n      <th>crp<\/th>\n      <th>mort<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":[1,3,6,7,8]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

``` r
tab1 <- twogrps(dt, gvar = "mort")
## tab1$Table ## 显示结果
```

|                |                 |               |                |          |
|:---------------|:----------------|:--------------|:---------------|:---------|
| Variables      | Total (n = 500) | 0 (n = 115)   | 1 (n = 385)    | p        |
| age, Mean ± SD | 70.53 ± 14.62   | 58.48 ± 11.43 | 74.13 ± 13.51  | \< 0.001 |
| gender, n (%)  |                 |               |                | 0.955    |
| female         | 188 (38)        | 44 (38)       | 144 (37)       |          |
| male           | 312 (62)        | 71 (62)       | 241 (63)       |          |
| lac, Mean ± SD | 4.76 ± 1.94     | 3.58 ± 1.77   | 5.08 ± 1.86    | \< 0.001 |
| type, n (%)    |                 |               |                | \< 0.001 |
| emergency      | 193 (39)        | 38 (33)       | 155 (40)       |          |
| medical        | 147 (29)        | 21 (18)       | 126 (33)       |          |
| surgery        | 160 (32)        | 56 (49)       | 104 (27)       |          |
| vaso, n (%)    |                 |               |                | \< 0.001 |
| No             | 353 (71)        | 102 (89)      | 251 (65)       |          |
| Yes            | 147 (29)        | 13 (11)       | 134 (35)       |          |
| wbc, Mean ± SD | 9.89 ± 4.67     | 10.37 ± 4.98  | 9.74 ± 4.57    | 0.271    |
| crp, Mean ± SD | 148.57 ± 74.24  | 105.31 ± 58.7 | 162.94 ± 73.36 | \< 0.001 |

这里的核心函数为`twogrps()`，进行两组间比较，该函数最主要的赋值为一个待分析的数据框*dt*和分组变量*mort*，数据框中除了分组变量外，其它变量都进行组间比较。

### 3.3 多组的比较

多组间比较我们换成了`multigrps()`函数，里面的参数赋值与前面完全一样。

``` r
tab2 <- multigrps(dt, gvar = "type")
## tab2$Table ## 显示结果
```

这样我们就完成了表格的制作，是不是特别简单？接下来我们再进一步体会一下**CBCgrps**其它有用的功能。

## 4 需要选择性比较变量

很多时候并不是所有的变量都要进行比较，这时候我们可以选择一部分变量进行比较，同时可以调整变量在表格中的显示顺序，用`varlist`参数实现该功能。

``` r
# 只比较4个变量，并且要求按照基本人口学特征（年龄、性别）、实验室检查（wbc）、治疗（vaso血管活性药物使用）的顺序显示
tabVarlist <- twogrps(dt, gvar = "mort", varlist = c("age", "gender", "wbc", "vaso"))
## tabVarlist$Table ## 显示结果
```

|                |                 |               |               |          |
|:---------------|:----------------|:--------------|:--------------|:---------|
| Variables      | Total (n = 500) | 0 (n = 115)   | 1 (n = 385)   | p        |
| age, Mean ± SD | 70.53 ± 14.62   | 58.48 ± 11.43 | 74.13 ± 13.51 | \< 0.001 |
| gender, n (%)  |                 |               |               | 0.955    |
| female         | 188 (38)        | 44 (38)       | 144 (37)      |          |
| male           | 312 (62)        | 71 (62)       | 241 (63)      |          |
| wbc, Mean ± SD | 9.89 ± 4.67     | 10.37 ± 4.98  | 9.74 ± 4.57   | 0.271    |
| vaso, n (%)    |                 |               |               | \< 0.001 |
| No             | 353 (71)        | 102 (89)      | 251 (65)      |          |
| Yes            | 147 (29)        | 13 (11)       | 134 (35)      |          |

如果要选择的变量很多，则可以先定义好要比较的变量，提高代码可读性。

``` r
# 这里显示顺序就是表格中出现的顺序，按照人口学特征、临床类型、实验室检查、治疗、结局这样的顺序基本符合一般临床思路。
varlist <- c("age", "gender", "type", "wbc", "lac", "vaso", "mort")
tabVarlist2 <- twogrps(dt, gvar = "mort", varlist = varlist)
## tabVarlist2$Table ## 显示结果
```

|                |                 |               |               |          |
|:---------------|:----------------|:--------------|:--------------|:---------|
| Variables      | Total (n = 500) | 0 (n = 115)   | 1 (n = 385)   | p        |
| age, Mean ± SD | 70.53 ± 14.62   | 58.48 ± 11.43 | 74.13 ± 13.51 | \< 0.001 |
| gender, n (%)  |                 |               |               | 0.955    |
| female         | 188 (38)        | 44 (38)       | 144 (37)      |          |
| male           | 312 (62)        | 71 (62)       | 241 (63)      |          |
| type, n (%)    |                 |               |               | \< 0.001 |
| emergency      | 193 (39)        | 38 (33)       | 155 (40)      |          |
| medical        | 147 (29)        | 21 (18)       | 126 (33)      |          |
| surgery        | 160 (32)        | 56 (49)       | 104 (27)      |          |
| wbc, Mean ± SD | 9.89 ± 4.67     | 10.37 ± 4.98  | 9.74 ± 4.57   | 0.271    |
| lac, Mean ± SD | 4.76 ± 1.94     | 3.58 ± 1.77   | 5.08 ± 1.86   | \< 0.001 |
| vaso, n (%)    |                 |               |               | \< 0.001 |
| No             | 353 (71)        | 102 (89)      | 251 (65)      |          |
| Yes            | 147 (29)        | 13 (11)       | 134 (35)      |          |
| mort, n (%)    |                 |               |               | \< 0.001 |
| 0              | 115 (23)        | 115 (100)     | 0 (0)         |          |
| 1              | 385 (77)        | 0 (0)         | 385 (100)     |          |

## 5 小数点位数的保留

上面的显示中我们发现年龄保留了两位小数，似乎没必要，一般年龄保留整数或者最多小数点后一位就行了。随手一段代码就可以搞定。

``` r
tabDecimal <- twogrps(dt, gvar = "mort", norm.rd = 1)
```

|                |                 |              |              |          |
|:---------------|:----------------|:-------------|:-------------|:---------|
| Variables      | Total (n = 500) | 0 (n = 115)  | 1 (n = 385)  | p        |
| age, Mean ± SD | 70.5 ± 14.6     | 58.5 ± 11.4  | 74.1 ± 13.5  | \< 0.001 |
| gender, n (%)  |                 |              |              | 0.955    |
| female         | 188 (38)        | 44 (38)      | 144 (37)     |          |
| male           | 312 (62)        | 71 (62)      | 241 (63)     |          |
| lac, Mean ± SD | 4.8 ± 1.9       | 3.6 ± 1.8    | 5.1 ± 1.9    | \< 0.001 |
| type, n (%)    |                 |              |              | \< 0.001 |
| emergency      | 193 (39)        | 38 (33)      | 155 (40)     |          |
| medical        | 147 (29)        | 21 (18)      | 126 (33)     |          |
| surgery        | 160 (32)        | 56 (49)      | 104 (27)     |          |
| vaso, n (%)    |                 |              |              | \< 0.001 |
| No             | 353 (71)        | 102 (89)     | 251 (65)     |          |
| Yes            | 147 (29)        | 13 (11)      | 134 (35)     |          |
| wbc, Mean ± SD | 9.9 ± 4.7       | 10.4 ± 5     | 9.7 ± 4.6    | 0.271    |
| crp, Mean ± SD | 148.6 ± 74.2    | 105.3 ± 58.7 | 162.9 ± 73.4 | \< 0.001 |

但这里会把所有符合正态分布的变量都保留一位小数，目前还不能进行逐个变量选择来保留小数点，如果需求大的话可以后续完善这个功能，敬请期待！ 同样对分类变量保留一位小数点也是轻而易举的事情。

``` r
tabCatDecimal <- twogrps(dt, gvar = "mort", cat.rd = 1)
```

|                |                 |              |              |          |
|:---------------|:----------------|:-------------|:-------------|:---------|
| Variables      | Total (n = 500) | 0 (n = 115)  | 1 (n = 385)  | p        |
| age, Mean ± SD | 70.5 ± 14.6     | 58.5 ± 11.4  | 74.1 ± 13.5  | \< 0.001 |
| gender, n (%)  |                 |              |              | 0.955    |
| female         | 188 (38)        | 44 (38)      | 144 (37)     |          |
| male           | 312 (62)        | 71 (62)      | 241 (63)     |          |
| lac, Mean ± SD | 4.8 ± 1.9       | 3.6 ± 1.8    | 5.1 ± 1.9    | \< 0.001 |
| type, n (%)    |                 |              |              | \< 0.001 |
| emergency      | 193 (39)        | 38 (33)      | 155 (40)     |          |
| medical        | 147 (29)        | 21 (18)      | 126 (33)     |          |
| surgery        | 160 (32)        | 56 (49)      | 104 (27)     |          |
| vaso, n (%)    |                 |              |              | \< 0.001 |
| No             | 353 (71)        | 102 (89)     | 251 (65)     |          |
| Yes            | 147 (29)        | 13 (11)      | 134 (35)     |          |
| wbc, Mean ± SD | 9.9 ± 4.7       | 10.4 ± 5     | 9.7 ± 4.6    | 0.271    |
| crp, Mean ± SD | 148.6 ± 74.2    | 105.3 ± 58.7 | 162.9 ± 73.4 | \< 0.001 |

## 6 p值的显示

默认状态下p值如果小于0.001就不现实具体数值了，这个我们也可以进行更改，比如小于0.05我们就不显示。

``` r
tabPshow <- twogrps(dt, gvar = "mort", p.rd = 2)
```

|                |                 |               |                |         |
|:---------------|:----------------|:--------------|:---------------|:--------|
| Variables      | Total (n = 500) | 0 (n = 115)   | 1 (n = 385)    | p       |
| age, Mean ± SD | 70.53 ± 14.62   | 58.48 ± 11.43 | 74.13 ± 13.51  | \< 0.01 |
| gender, n (%)  |                 |               |                | 0.95    |
| female         | 188 (38)        | 44 (38)       | 144 (37)       |         |
| male           | 312 (62)        | 71 (62)       | 241 (63)       |         |
| lac, Mean ± SD | 4.76 ± 1.94     | 3.58 ± 1.77   | 5.08 ± 1.86    | \< 0.01 |
| type, n (%)    |                 |               |                | \< 0.01 |
| emergency      | 193 (39)        | 38 (33)       | 155 (40)       |         |
| medical        | 147 (29)        | 21 (18)       | 126 (33)       |         |
| surgery        | 160 (32)        | 56 (49)       | 104 (27)       |         |
| vaso, n (%)    |                 |               |                | \< 0.01 |
| No             | 353 (71)        | 102 (89)      | 251 (65)       |         |
| Yes            | 147 (29)        | 13 (11)       | 134 (35)       |         |
| wbc, Mean ± SD | 9.89 ± 4.67     | 10.37 ± 4.98  | 9.74 ± 4.57    | 0.27    |
| crp, Mean ± SD | 148.57 ± 74.24  | 105.31 ± 58.7 | 162.94 ± 73.36 | \< 0.01 |

## 7 正态分布的判定

默认状态下twogrps函数对连续变量进行正态性检验，但有时候样本量巨大，正态性检验用常规的0.05作为界值会过于敏感（只有一点点差异也被判定为不符合正态分布，理论上讲样本量如果无限的话，基本上就不存在符合正态分布的连续变量了）。这时候我们可以人为设定那些变量是非正态分布的，从而采用中位数（四分位间距）来进行统计描述。

``` r
tabNormTest <- twogrps(dt, gvar = "mort", skewvar = c("crp", "wbc")) #人为设定crp和wbc为非正态分布
```

|                     |                     |                       |                      |          |
|:--------------------|:--------------------|:----------------------|:---------------------|:---------|
| Variables           | Total (n = 500)     | 0 (n = 115)           | 1 (n = 385)          | p        |
| age, Mean ± SD      | 70.53 ± 14.62       | 58.48 ± 11.43         | 74.13 ± 13.51        | \< 0.001 |
| gender, n (%)       |                     |                       |                      | 0.955    |
| female              | 188 (38)            | 44 (38)               | 144 (37)             |          |
| male                | 312 (62)            | 71 (62)               | 241 (63)             |          |
| lac, Mean ± SD      | 4.76 ± 1.94         | 3.58 ± 1.77           | 5.08 ± 1.86          | \< 0.001 |
| type, n (%)         |                     |                       |                      | \< 0.001 |
| emergency           | 193 (39)            | 38 (33)               | 155 (40)             |          |
| medical             | 147 (29)            | 21 (18)               | 126 (33)             |          |
| surgery             | 160 (32)            | 56 (49)               | 104 (27)             |          |
| vaso, n (%)         |                     |                       |                      | \< 0.001 |
| No                  | 353 (71)            | 102 (89)              | 251 (65)             |          |
| Yes                 | 147 (29)            | 13 (11)               | 134 (35)             |          |
| wbc, Median (Q1,Q3) | 9.8 (6.7, 13.2)     | 10.8 (6.25, 14.65)    | 9.7 (6.9, 12.7)      | 0.229    |
| crp, Median (Q1,Q3) | 146.7 (95.6, 201.7) | 110.9 (63.15, 143.02) | 164.7 (107.5, 213.4) | \< 0.001 |

## 8 显示统计量

国内很多杂志要求显示统计量（如T值、F值等），但国外好像还没碰到这样的杂志，一般只要求显示p值即可。不管怎样，对于**CBCgrps**来说也是so easy！

``` r
tabStatistic <- twogrps(dt, gvar = "mort", ShowStatistic = T)
```

|                |                 |               |                |          |           |
|:---------------|:----------------|:--------------|:---------------|:---------|:----------|
| Variables      | Total (n = 500) | 0 (n = 115)   | 1 (n = 385)    | p        | statistic |
| age, Mean ± SD | 70.53 ± 14.62   | 58.48 ± 11.43 | 74.13 ± 13.51  | \< 0.001 | -12.333   |
| gender, n (%)  |                 |               |                | 0.955    | 0.003     |
| female         | 188 (38)        | 44 (38)       | 144 (37)       |          |           |
| male           | 312 (62)        | 71 (62)       | 241 (63)       |          |           |
| lac, Mean ± SD | 4.76 ± 1.94     | 3.58 ± 1.77   | 5.08 ± 1.86    | \< 0.001 | -7.343    |
| type, n (%)    |                 |               |                | \< 0.001 | 20.507    |
| emergency      | 193 (39)        | 38 (33)       | 155 (40)       |          |           |
| medical        | 147 (29)        | 21 (18)       | 126 (33)       |          |           |
| surgery        | 160 (32)        | 56 (49)       | 104 (27)       |          |           |
| vaso, n (%)    |                 |               |                | \< 0.001 | 22.443    |
| No             | 353 (71)        | 102 (89)      | 251 (65)       |          |           |
| Yes            | 147 (29)        | 13 (11)       | 134 (35)       |          |           |
| wbc, Mean ± SD | 9.89 ± 4.67     | 10.37 ± 4.98  | 9.74 ± 4.57    | 0.271    | 1.105     |
| crp, Mean ± SD | 148.57 ± 74.24  | 105.31 ± 58.7 | 162.94 ± 73.36 | \< 0.001 | -7.806    |

  
