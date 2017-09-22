---
title: CSS3自定义浏览器滚动条样式
date: 2016-12-21 17:37:12
categories: CSS
tags: css3
description: 本文首发于 http://liuxianan.com，原文地址：http://blog.liuxianan.com/css-scrollbar.html，转载请注明署名“liuxianan”并在显眼位置保留原文链接，谢谢！
author: liuxianan
---

# 说明

非标准属性，仅限webkit内核浏览器。

# 组成部分

一个完整滚动条右以下部分组成：

1. `::-webkit-scrollbar` 滚动条整体部分，常用属性：width,height,background,border；
2. `::-webkit-scrollbar-button` 滚动条两边的按钮，默认不设置时不显示，可设置高度、背景色、背景图片；
3. `::-webkit-scrollbar-track` 整个滚动条去除两边按钮剩下的部分；
4. `::-webkit-scrollbar-track-piece`  track去掉拖拽剩下的部分；
5. `::-webkit-scrollbar-thumb` 滚动条里面可以拖动的那部分；
6. `::-webkit-scrollbar-corner` 边角；
7. `::-webkit-resizer` 定义右下角拖动块的样式

借用一张网上挺不错的图片说明：

![](http://image.liuxianan.com/201612/20161221_172336_952_5808.png)

# DEMO

简单演示DEMO，不好看：

```html
<style>
/* 设置整个滚动条的一些属性，宽度针对垂直滚动条，高度针对水平滚动条 */
::-webkit-scrollbar {
	width: 12px;
	height: 12px;
}
/* 滚动条顶部按钮的样式，如果不设置默认按钮不显示 */
::-webkit-scrollbar-button {
	background-color: #FF945B;
}
::-webkit-scrollbar-button:hover {
	background-color: #EF6A22;
}
::-webkit-scrollbar-button:active {
	background-color: #B94608;
}
/* 整个滚动条去除button剩下的部分 */
::-webkit-scrollbar-track {
	background: #D9DDD3;
}
/* 滚动条可拖拽的部分 */
::-webkit-scrollbar-thumb {
	border-radius: 5px;
	background-color: rgba(178, 178, 178, 0.69);
}
::-webkit-scrollbar-thumb:hover {
	background-color: #aaa;
}
::-webkit-scrollbar-thumb:active {
	background-color: #666363;
}
.sample {
    width: 600px;
    height: 400px;
    overflow: auto;
}
.sample-wrapper {
	width: 1200px;
	height: 1000px;
	background: -webkit-linear-gradient(red, blue);
    background: linear-gradient(red, blue);
	color: white;
}
</style>
<div class="sample">
	<div class="sample-wrapper">
		<p>测试滚动示例1</p>
		<p>测试滚动示例2</p>
		<p>测试滚动示例3</p>
		<p>测试滚动示例4</p>
		<p>测试滚动示例5</p>
		<p>测试滚动示例6</p>
		<p>测试滚动示例7</p>
		<p>测试滚动示例8</p>
	</div>
</div>
```

稍微好看点的例子：

```html
<style>
/* 设置整个滚动条的一些属性，宽度针对垂直滚动条，高度针对水平滚动条 */
::-webkit-scrollbar {
    width: 10px;
    height: 10px;
}
/* 整个滚动条去除button剩下的部分 */
::-webkit-scrollbar-track {
    border-radius: 10px;
    background-color: #d8dce5
}
/* 滚动条可拖拽的部分 */
::-webkit-scrollbar-thumb {
    border-radius: 5px;
    background-color: #adadad;
}
::-webkit-scrollbar-thumb:hover {
    backg