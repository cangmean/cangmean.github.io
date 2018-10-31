---
title: css 选择器
date: 2018-08-03 14:57:32
tags:
    - css
categories:
    - 前端
    - css
---

css 通过选择器精确定位 HTML 元素。我们可以通过 css 选择器改变 HTML 的样式.
```css
div {
  background-color: red;
}
```

`div`是一个选择器， 紧跟着 css 的声明块， 其中 `background-color`表示属性， `red`表示属性的值.

<!--more-->

### 选择器类型

选择器分为一下几个类别:

| 选择器     | 描述                                                           |
| ---------- | -------------------------------------------------------------- |
| 简单选择器 | 通过元素类型， `class`或`id`匹配元素                           |
| 属性选择器 | 通过元素属性/属性值匹配元素                                    |
| 伪类       | 匹配处于确定状态的一个或多个元素，比如被鼠标指针悬停的元素     |
| 伪元素     | 匹配处于相关的确定位置的一个或多个元素，例如每个段落的第一个字 |
| 组合器     | 组合多个选择器                                                 |
| 多重选择器 | 将以逗号分隔开的多个选择器放在一个CSS规则下面                  |

#### 元素选择器

元素选择器通过HTML元素， 来改变节点样式
{% jsfiddle ko058hLu "html,css,result" "dark" 100% 250px %}

#### 类选择器
类选择器不考虑具体的元素， 而是通过HTML中指定的class来选择节点. 类选择器通过`.<类名>` 表示
{% jsfiddle 1nvumLdh "html,css,result" "dark" 100% 250px %}

#### ID选择器
ID选择器跟类选择器类似， 不过节点中ID值不能有重复
{% jsfiddle cefxsnw2 "html,css,result" "dark" 100% 250px %}


### 属性选择器
属性选择器类似类选择器和ID选择器, 他可以根据属性和属性值来选择元素
{% jsfiddle tjfo7y3L "html,css,result" "dark" 100% 250px %}
这个例子把所有包含class属性的节点字体改为蓝色

#### 根据具体属性值来选择
属性选择器还可以通过具体属性值来选择元素
{% jsfiddle ed4v2gwt "html,css,result" "dark" 100% 250px %}

#### 根据部分属性值来选择
选择属性值中的一部分
{% jsfiddle uryakp3n "html,css,result" "dark" 100% 250px %}

#### 子串属性选择器
| 类型          | 描述                                     |
| ------------- | ---------------------------------------- |
| [foot^="bar"] | 选择属性值foo属性值以"bar"开头的所有属性 |
| [foot$="bar"] | 选择属性值foo属性值以"bar"结尾的所有属性 |
| [foot*="bar"] | 选择属性值foo属性值中包含"bar"的所有属性 |

### 伪类
伪类通过html元素的状态来选择， 以下是常用的伪类

| 伪类名   | 描述                     |
| -------- | ------------------------ |
| :link    | 指向作为未访问地址       |
| :visited | 指向作为已访问地址       |
| :focus   | 指示当前用户输入焦点     |
| :hover   | 鼠标指针停留在元素节点上 |
| :active  | 用户鼠标点击元素         |

伪类通常作用在链接和输入框中， 来动态改变元素的样式
```CSS
a:link {color: navy;}
a:visited {color: gray;}
a:hover {color:red;}
a:active {color: yellow;}
```

### 伪元素
伪元素可以再文档中插入假想的元素, 从而得到某种效果
{% jsfiddle r5fq8xho "html,css,result" "dark" 100% 250px %}

### 组合器
可以组合多个选择器来选择节点
#### 后代选择器
后代选择器选择h1节点下的所有em元素

```CSS
h1 em { color:gray;}
```

#### 子选择器
子选择器选择直系后代中所有em元素
```CSS
h1 > em { color: gray;}
```
#### 相邻选择器
p是h1之后紧跟这的节点(h1和p共同的父元素)
```CSS
h1 + p {
  margin-top: 0;
}
```
#### 兄弟选择器
p是h1之后的兄弟节点中的任意元素
```CSS
h1 ~ p {
  margin-top: 0;
}
```
### 多重选择器

将多个有相同规则的选择器以`,`组合在一起
```CSS
h1, h2, h3, h4, h5, h6 {
  font-family: helvetica, 'sans serif';
}
```

### 参考 (本文例子全部来至于MDN)
  - css权威指南
  - [MDN](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Introduction_to_CSS/Selectors)
