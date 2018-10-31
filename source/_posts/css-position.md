---
title: css 定位属性
date: 2018-08-10 11:46:30
tags:
  - css
categories:
  - 前端
  - css
---

元素的 position 属性使元素在文档布局中进行定位。

### 定位属性

static 属性是 position 的默认属性，另外还有 relative, absolute, fixed, inherit。定义 position 属性以后可以通过 top, left, bottom, right 控制元素在文档中的位置

#### static 默认定位

static 属性是 posistion 的默认属性，元素出现在文档流中 top, left, bottom, right 属性无效

#### relative 相对定位

relative 属性使元素相对自身进行定位， 元素不脱离文档流

<!--more-->

{% jsfiddle 3ao2fytj "html,css,result" "dark" 100% 250px %}

这个例子中因为红色 div 虽然位置发生了变化，但是没有脱离文档流，因此黄色 div 不会去填补空位。

#### absolute 绝对定位

absolute 根据 position 属性是 static 以外的第一个父节点进行定位，元素脱离文档流
{% jsfiddle 08djsf2h "html,css,result" "dark" 100% 250px %}

这个例子中因为红色 div 设置为 absolute 所以脱离了文档流，黄色 div 填补了空白的位置。如果 absolute 找不到定位父元素会一直往上找，直到根节点
{% jsfiddle vo86mb5h "html,css,result" "dark" 100% 250px %}
我们给灰色 div 设置定位属性以后，红色 div 定位变化为相对灰色 div，而不是根节点

#### fixed 固定定位

fixed 定位是根据 body 进行定位，即使页面滚动，元素位置仍不变
{% jsfiddle mhd8r1pt "html,css,result" "dark" 100% 250px %}

### 定位点

left, top, right, bottom 属性会为定位元素(脱离文档流元素)设置定位点和偏移位置。

#### 左上角

默认情况下，定位元素位置为左上角， 等于设置了`left:0` 和 `top:0`
{% jsfiddle gdkrjo63 "html,css,result" "dark" 100% 250px %}

#### 左下角

这里我们设置了定位元素的位置为左下角，位置向左偏移了 10px
{% jsfiddle sfck6rpd "html,css,result" "dark" 100% 250px %}
