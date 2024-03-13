---
title: CSS
date: 2021-07-03 10:41:49
tags: 
---

HTML可以让网页具有基本的可读性。使用CSS可以完全控制浏览器如何显示html元素，CSS是一门基于规则的语法。

语法由选择器开头，紧接着一个大括号，里面包含一个多个声明，每个声明是由属性和值构成，冒号前是属性，后是值，以分号结尾。

# 添加CSS

在html文档中链接CSS文件，在`<head>`中链接。

```html
<link rel="stylesheet" herf="./style.css">
```

用`rel`属性表明css文档的，用`href`表明css文档的位置。

# 如何定位元素

## 选择器

可以用逗号分割开不同的选择器，即使一次使用多个选择器

```css
p, li{
	color: green;
}
```

### 包含选择符的选择器

当在不同地方有多个元素时，我们可以在使用包含选择符的选择器，他是只是单纯的在两个选择器之间加入一个空格。

```css
li em {
    color: rebeccapurple;
}
```

这仅仅是嵌套在列表中的em元素。

### 相邻选择符

```css
h1 + p {
    font-size: 200%;
}
```

紧跟着`<h1>`后出现的`<p>`。

## 改变元素默认行为

浏览器有一套默认的样式表，但我们也可以改变这些样式。比如去掉无序列表前的点

```css
ul {
    list-style-type: none;
}
```

将列表前的点改为方块

```css
li {
    list-style-type: square;
}
```

## 使用类名

我们可以将html加上类名，通过css选中类型来单独样式化这一片html元素。

我们在html的标签中加入`class="special"`在css中通过西文点来访问标签，就可以单独对类进行样式化。

类名可以重复，可以在多个地方使用，因此还有一种html元素和选择器同时出现的写法，作用是选择在这个元素中的类。

```css
li.special {
    color: orange;
    font-weight: bold;
}
```

