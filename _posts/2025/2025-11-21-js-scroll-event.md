---
layout: post
title: "JavaScript 页面滚动事件（scroll）详解与实战"
author: Lele
date: 2025-11-21
categories: [前端开发]
tags: [javascript, scroll事件, 页面滚动, DOM操作, 前端实战]
excerpt: 详解 JavaScript scroll 事件的用法，包括监听页面/元素滚动、获取滚动位置（scrollTop/scrollLeft），并通过实战案例演示滚动显示元素的实现逻辑。
---

## 什么是 scroll 事件？
`scroll` 事件是 JavaScript 中用于检测**元素滚动行为**的核心事件，当元素的滚动条位置发生变化时会立即触发。其应用场景灵活，支持两种绑定目标：
- 绑定到 `window` 对象：监听整个页面的滚动行为（最常用场景）；
- 绑定到带滚动条的 DOM 元素：监听特定容器的内部滚动（如滚动列表、弹窗内容区）。

常见实际应用：固定导航栏、滚动显示返回顶部按钮、滚动加载更多数据、滚动动画触发等。

## 基础语法
### 1. 监听页面滚动（window 绑定）
```javascript
// 方式1：匿名函数回调
window.addEventListener('scroll', function() {
  // 页面滚动时执行的逻辑（如获取滚动位置、触发动画等）
  console.log('页面滚动中...');
});

// 方式2：单独定义回调函数（便于后续移除事件监听）
function handlePageScroll() {
  console.log('页面滚动距离：', document.documentElement.scrollTop);
}
window.addEventListener('scroll', handlePageScroll);

// 移除事件监听（需要使用命名函数）
// window.removeEventListener('scroll', handlePageScroll);
```
## 案例：滚动显示元素
### HTML+CSS代码
```html
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
            position: fixed;
  /* 定位坐标：距离顶部20px、右侧20px */
            top: 20%;
           right: 40%;
           background: white;
  /* 水平右移3px、垂直下移3px、模糊5px、灰色阴影 */
           box-shadow: 3px 3px 5px #e0e0e0;
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
</body>
</html>
```
### JavaScript代码
```javascript
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
```
