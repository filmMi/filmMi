---
title: JQuery实现懒图片加载
date: 2018-08-08 13:43:03
tags: JQuery
---

在网页中，常常需要用到图片，而图片需要消耗较大的流量。正常情况下，浏览器会解析整个HTML代码，然后从上到下依次加载`<img src="xxx">`的图片标签。如果页面很长，隐藏在页面下方的图片其实已经被浏览器加载了。如果用户不向下滚动页面，就没有看到这些图片，相当于白白浪费了图片的流量。

所以，淘宝、京东这些流量非常巨大的电商，商品介绍页又必须有大量的图片，因此，这些页面的图片都是“按需加载”，即用户滚动页面时显示出来的时候才加载图片。当网速非常快的时候，用户并不能感知懒加载的动作，既省流量又不影响用户浏览。

本文给出一种利用jQuery实现图片懒加载的原理。它的基本思想是：在输出HTML的时候，不要直接输出`<img src="xxx"`，而是输出如下的img标签： 
``` bash
<img src="/static/loading.gif" data-src="http://真正的图片地址/xxx.jpg">
```
因此，页面显示的图片是一个gif加载动画。当页面滚动时，如果图片出现在屏幕中，就利用jQuery把`<img>`的src属性替换为data-src的内容，浏览器就会实时加载。
为了更好的体验，初始加载中给src一个默认的gif图片。
JavaScript代码如下
```bash
// 注意: 需要引入jQuery和underscore
$(function() {
    // 获取window的引用:
    var $window = $(window);
    // 获取包含data-src属性的img，并以jQuery对象存入数组:
    var lazyImgs = _.map($('img[data-src]').get(), function (i) {
        return $(i);
    });
    // 定义事件函数:
    var onScroll = function() {
        // 获取页面滚动的高度:
        var wtop = $window.scrollTop();
        // 判断是否还有未加载的img:
        if (lazyImgs.length > 0) {
            // 获取可视区域高度:
            var wheight = $window.height();
            // 存放待删除的索引:
            var loadedIndex = [];
            // 循环处理数组的每个img元素:
            _.each(lazyImgs, function ($i, index) {
                // 判断是否在可视范围内:
                if ($i.offset().top - wtop < wheight) {
                    // 设置src属性:
                    $i.attr('src', $i.attr('data-src'));
                    // 添加到待删除数组:
                    loadedIndex.unshift(index);
                }
            });
            // 删除已处理的对象:
            _.each(loadedIndex, function (index) {
                lazyImgs.splice(index, 1);
            });
        }
    };
    // 绑定事件:
    $window.scroll(onScroll);
    // 手动触发一次:
    onScroll();
```
`onScroll()`函数最后要手动触发一次，因为页面显示时，并未触发scroll事件。如果图片已经在可视区域内，这些图片仍然是loading状态，需要手动触发一次，就可以正常显示。
要测试图片懒加载效果，可以在Chrome浏览器的控制台选择“Network”，把“Online”改为“Slow 3G”就可以模拟慢速网络下浏览器懒加载图片的效果。
下面贴上完整测试的代码
```bash
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>ssss</title>
    
    <style>
        ul,li{
            list-style:none;
        }
        .container{
            width: 600px;
            margin: 0 auto;
        }
        .container li{
            float: left;
            margin: 10px 10px;
        }
        .container li img{
            width: 240px;
            height: 180px;
        }
        p{
            float: left;
        }
    </style>
</head>
<body>
<ul class="container">
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/1.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/2.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/3.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/4.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/5.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/6.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/7.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/8.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/9.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/10.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/11.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/12.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/13.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/14.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/15.jpg" /></a></li>
    <li><a href="#"><img src="" data-src="http://cdn.jirengu.com/book.jirengu.com/img/16.jpg" /></a></li>
</ul>
<script src="http://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
<script src="https://cdn.bootcss.com/underscore.js/1.9.0/underscore-min.js"></script>
<script>
    // 注意: 需要引入jQuery和underscore
$(function() {
    // 获取window的引用:
    var $window = $(window);
    // 获取包含data-src属性的img，并以jQuery对象存入数组:
    var lazyImgs = _.map($('img[data-src]').get(), function (i) {
        return $(i);
    });
    // 定义事件函数:
    var onScroll = function() {
        // 获取页面滚动的高度:
        var wtop = $window.scrollTop();
        // 判断是否还有未加载的img:
        if (lazyImgs.length > 0) {
            // 获取可视区域高度:
            var wheight = $window.height();
            // 存放待删除的索引:
            var loadedIndex = [];
            // 循环处理数组的每个img元素:
            _.each(lazyImgs, function ($i, index) {
                // 判断是否在可视范围内:
                if ($i.offset().top - wtop < wheight) {
                    // 设置src属性:
                    $i.attr('src', $i.attr('data-src'));
                    // 添加到待删除数组:
                    loadedIndex.unshift(index);
                }
            });
            // 删除已处理的对象:
            _.each(loadedIndex, function (index) {
                lazyImgs.splice(index, 1);
            });
        }
    };
    // 绑定事件:
    $window.scroll(onScroll);
    // 手动触发一次:
    onScroll();
})
</script>
</body>
</html>
```