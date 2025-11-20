---
layout: post
title: "JavaScript 页面滚动事件（scroll）详解与实战"
author: Lele
tags: [javascript, scroll事件, 页面滚动, DOM操作]
excerpt: >
  详解 scroll 事件的用法，包括监听页面滚动、获取滚动位置（scrollTop/scrollLeft）及实战场景（滚动显示元素）。
cover: /assets/images/posts/js-scroll-cover.png
---

## 什么是 scroll 事件？
`scroll` 事件是 JavaScript 中用于检测**元素滚动行为**的事件，当元素的滚动条位置发生变化时触发。  
- 可绑定到 **window**（监听整个页面滚动）；  
- 可绑定到**带滚动条的元素**（如 `<div style="overflow: scroll">`）。  

常见应用场景：固定导航栏、滚动显示返回顶部按钮、滚动加载更多内容等。


## 基础语法
```javascript
// 监听页面滚动（window 绑定）
window.addEventListener('scroll', function() {
  // 滚动时执行的逻辑
  console.log('页面滚动了');
});

// 监听元素滚动（带滚动条的元素绑定）
const scrollBox = document.querySelector('.box');
scrollBox.addEventListener('scroll', function() {
  // 元素滚动时执行的逻辑
  console.log('元素滚动了');
});

## 核心属性
scrollTop: 滚动条的垂直滚动距离
scrollLeft: 滚动条的水平滚动距离

## 案例：实现当页面滚动到一定位置显示文本框

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>scroll 事件实战</title>
    <style>
        body {
            height: 1500px; /* 让页面可滚动 */
            margin: 0;
            padding: 20px;
        }

        .box {
            display: none; /* 默认隐藏 */
            margin: 50px auto;
            width: 200px;
            height: 200px;
            background-color: palevioletred;
            overflow: scroll; /* 元素自身带滚动条 */
            padding: 10px;
            border-radius: 4px;
        }
    </style>
</head>
<body>
    <!-- 带滚动条的元素 -->
    <div class="box">
        我里面有很多文字<br>
        我里面有很多文字<br>
        我里面有很多文字<br>
        我里面有很多文字<br>
        我里面有很多文字<br>
        我里面有很多文字<br>
        我里面有很多文字<br>
        我里面有很多文字<br>
        我里面有很多文字<br>
        我里面有很多文字<br>
    </div>

    <script>
        // 1. 监听页面滚动（window）
        window.addEventListener('scroll', function() {
            console.log('页面滚动了');
        });

        // 2. 监听元素滚动（.box）并获取滚动位置
        const box = document.querySelector('.box');
        box.addEventListener('scroll', function() {
            console.log('元素垂直滚动距离：', this.scrollTop); // 垂直滚动距离
            console.log('元素水平滚动距离：', this.scrollLeft); // 水平滚动距离（本例为0）
        });

        // 3. 实战：页面滚动到100px时显示.box，否则隐藏
        // 初始让页面滚动到200px位置（测试用）
        document.documentElement.scrollTop = 200;

        // 监听页面滚动
        window.addEventListener('scroll', function() {
            // 获取页面垂直滚动距离（document.documentElement 对应 html 根元素）
            const scrollDistance = document.documentElement.scrollTop;

            // 根据滚动距离控制 .box 显示/隐藏
            if (scrollDistance >= 100) {
                box.style.display = 'block';
            } else {
                box.style.display = 'none';
            }
        });
    </script>
</body>
</html>

##总结

滚动位置控制：
页面滚动距离通过 document.documentElement.scrollTop 获取（兼容主流浏览器）；
元素滚动距离通过 元素.scrollTop 获取；
可通过修改 scrollTop 强制设置滚动位置（如 document.documentElement.scrollTop = 200）。