---
title: css 盒模型
date: 2018-08-07 17:56:07
tags:
  - css
categories:
  - 前端
  - css
---

### CSS 盒模型

css 中使用盒模型来描述元素所占空间的内容。分别是内容区域、内边框、边框、外边框。以下是盒模型图：

![CSS盒模型](https://mink.oss-cn-beijing.aliyuncs.com/blog/box-model.svg)

<!--more-->

- **内容区域** 包含真是的元素内容。包括文字图片，一般情况下设置 width，height 改变的就是这个区域.
- **内边框** 内边框是内容区域和边框之间的部分， `padding`来改变周围的所占空间.
- **边框** 可以通过`border`属性来改变
- **外边框** 外边框是边框以外的部分，以分开相邻部分元素，可以通过`margin`属性来设置

### box-sizing

默认情况下，我们为某一个元素设定宽高，是设置 content-box 区域的内容，当我们设置 margin 和 padding 的时候元素所占空间会变大

{% jsfiddle y412gb63 "html,css,result" "dark" 100% 250px %}

例子中设置了 margin 和 padding 后，黄色区域的宽度变为 110px，加上左边的外间距，实际所占空间为 120px。我们可以通过 box-sizing 属性指定 width 和 height 控制的区域包含边框和内边框

{% jsfiddle kw3y89ts "html,css,result" "dark" 100% 250px %}

元素设置`box-sizing: border-box`后，元素所占区域变为 110px，而元素的 content-box 从原来的 100px 变为 90px
