---
title: css float 浮动
date: 2018-08-17 14:59:09
tags:
  - css
categories:
  - 前端
  - css
---

在网页布局中除了 position 属性以外最常用的就是 float 属性，使用 float 属性的元素会脱离标准文档流，并根据 float 属性进行左右浮动。

### 使用 float 属性

多个 div 元素根据文档流会呈现层级结构

{% jsfiddle fy47djhg "html,css,result" "dark" 100% 250px %}

当我们给 yellow 设置浮动属性以后，就脱离了文档流 red 填充原有 yellow 所占的位置

<!--more-->

{% jsfiddle 5puyk6Lh "html,css,result" "dark" 100% 250px %}

当我们给 red 也设置浮动属性后，red 紧跟着 yellow

{% jsfiddle 3r9mdtez "html,css,result" "dark" 100% 250px %}

我们设置新的 gray 部分，并且设置为行级标签 span，也设置浮动属性

{% jsfiddle 71ysften "html,css,result" "dark" 100% 250px %}

可以看出当我们给 gray 设置浮动后，gray 变成了块级元素，而且 gray 也没有跟在 yellow 元素后面，因为上一个 red 是在标准流中所以 gray 保证自己的顶部与 red 元素底部对齐

### 清除浮动

很多时候我们为子元素设置浮动后，发现父元素所占空间高度为空，这是因为浮动元素脱离文档流后不占空间所导致的，这时候需要清除浮动

**方法一**
使用`clear:both`属性清除浮动

```html
<div>
  ...浮动元素
  <div style="clear:both"></div>
</div>
```

`clear`属性只能影响自身，也就是清楚了 div 两边的浮动，缺点是需要多写一个 div 元素

**方法二**
父元素设置`overflow:auto`属性清除浮动

```html
<div style="overflow: auto;">
  ...浮动元素
</div>
```

`overflow`可以使用`auto`和`hidden`来清除浮动

**方法三**
通过`:after`伪元素来清除浮动

```css
div:after {
  display: block;
  clear: both;
  content: '';
  height: 0;
}
```
