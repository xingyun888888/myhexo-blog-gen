---
title: 如何在swiper内制作css3动画效果
date: 2017-12-01 17:37:12
categories: CSS
tags: css3
---

[以下内容参考链接](http://www.swiper.com.cn/usage/animate/index.html)

- ####  在需要添加动画的页面里面引入以下几个文件 版本对应

```
<script src="../js/swiper.min.js"></script>
<script src="../js/swiper.animate.min.js"></script>
//**这里引入jquery或者zepto.js都可以**//
<script src="../js/jquery-1.9.1.js"></script>
<link rel="stylesheet" href="../css/animate.min.css">

```

- ##### 接着在页面js部分添加(按业务需求)

```
var mySwiper = new Swiper('.swiper-container',{
          autoplay : 5000,//自动切换时间
          pagination : '.swiper-pagination',
          //pagination : '#swiper-pagination1',
          onInit: function(swiper) {//轮播初始化时候执行动画
              swiperAnimateCache(swiper);
              swiperAnimate(swiper);
          },        
          onSlideChangeEnd: function(swiper) {//轮播切换到最后一张的时候重新执行
              swiperAnimate(swiper);
          }
      })
```
- ##### 在需要执行的动画的元素上面添加class:

```
  给需要执行动画的元素上添加class ("ani"、"animated")  
  
  然后可以添加animate.css里面提供的一些动画
  
  如果animate.css里面的动画不能满足需求 也可以自定义一些动画
  
  直接在执行动画的元素对应的css里面添加自定义的动画样式
  
  还可以在元素上面配置一下几个参数
  
  swiper-animate-effect：切换效果，例如 fadeInUp 
  swiper-animate-duration：可选，动画持续时间（单位秒），例如 0.5s
  swiper-animate-delay：可选，动画延迟时间（单位秒），例如 0.3s
 
```

- ##### 下面是案例

![](https://user-gold-cdn.xitu.io/2017/11/26/15ff886931f765d5?w=982&h=118&f=png&s=13669)




>作者：xingyun