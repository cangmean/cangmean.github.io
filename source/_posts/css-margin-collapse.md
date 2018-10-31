---
title: css 外边距合并
date: 2018-08-07 19:52:36
tags:
  - css
categories:
  - 前端
  - css
---

### 问题

当两个块级元素的上外边距和下外边距发生合并为一个外边距，取值为其中的最大值，这是情况叫**外边距合并**或**margin 崩塌 折叠**

### 什么时候发生崩塌

> 相邻的两个节点的外边距会合并

<!--more-->

下面例子中两个相邻的元素的外边距发生合并
{% jsfiddle yqvbgs32 "html,css,result" "dark" 100% 250px %}

> 父元素与其第一个或最后一个子元素之间

下面例子中子元素的 margin 溢出并覆盖了父元素的 margin
{% jsfiddle 91p8y6zh "html,css,result" "dark" 100% 250px %}

> 空的块级元素

可以通过盒模型查看，空的元素发生合并
{% jsfiddle Lxpgvw7t "html,css,result" "dark" 100% 250px %}

### 解决崩塌

- 浮动元素不会和任何元素发生折叠。
- 创建 BFC 元素不会和他的子元素发生折叠。
- 绝对定位元素不会和其他元素发生折叠。
- inline-block 元素不会和其他元素发生折叠。

#### 浮动元素解决崩塌

设置浮动元素可以避免 margin 崩塌，元素之间的间隔重新变为 30px
{% jsfiddle k5fu7dvx "html,css,result" "dark" 100% 250px %}

#### 创建 BFC

BFC(块格式化上下文)是布局过程中生成块级盒子的区域，也是浮动元素与其他元素的交互限定区域。

**以下方法创建 BFC**

- 根元素或包含根元素的元素
- 浮动元素(float 不是 none)
- 绝对定位元素(position 为 absolut 和 fixed)
- display 属性为 inline-block 和与 table 相关的一系列属性
- overflow 不为 visible

创建了块格式化上下文的元素中的所有内容都会被包含到该 BFC 中。

### 参考

- https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing
- https://segmentfault.com/a/1190000010346113
