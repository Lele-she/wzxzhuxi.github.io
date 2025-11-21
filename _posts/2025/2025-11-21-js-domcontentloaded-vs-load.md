---
layout: post
title: "深入理解 JavaScript 中的 DOMContentLoaded 与 load 事件"
author: Lele
tags: [javascript, dom事件, 页面加载]
excerpt: >
  详解 DOMContentLoaded 与 load 事件的区别、触发时机及使用场景，帮你精准控制页面加载后的逻辑执行时机
---

## 核心问题：页面加载后，代码何时执行？
在 JavaScript 中，页面加载过程会触发多个事件，其中 **DOMContentLoaded** 和 **load** 是最常用的两个，但二者触发时机差异极大，误用可能导致功能异常（如操作未渲染的 DOM、等待不必要的资源）。

本文通过实战示例，清晰区分二者的核心区别与适用场景。

## 事件核心区别
**DOMContentLoaded** 事件：
DOM 结构加载解析完成（无需等待图片、样式表等资源） | 需操作 DOM 元素的逻辑（如绑定事件、修改节点） 
**load** 事件：
整个页面所有资源（DOM、图片、脚本、样式表）加载完成 | 需依赖资源的逻辑（如获取图片尺寸、统计页面加载完成时间）

## 案例：模拟 DOMContentLoaded 与 load 事件
### 完整 HTML+CSS结构
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DOMContentLoaded vs load 事件对比</title>
    <style>
        body {
            height: 3000px; /* 模拟长页面，便于观察加载过程 */
            background-color: #f5f5f5;
            padding: 20px;
        }

        .demo-container {
            max-width: 800px;
            margin: 0 auto;
            background: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }

         .demo-image {
            width: 800px;
            height: 700px;
            margin: 20% 0;
        }
        .demo-image img{
            width: 100%;
            height: 100%;
        }
    </style>
</head>
<body>
    <div class="demo-container">
        <h2>DOMContentLoaded 与 load 事件演示</h2>
        <p>打开浏览器控制台（F12），查看事件触发顺序</p>
        <!-- 模拟大图片，制造加载延迟 -->
      <div class="demo-image" ><img src="/assets/images/posts/2025-11-21-js-domcontentloaded-vs-load/len-view.png" alt=""></div>
    </div>
</body>
</html>
```
### 完整 JS脚本
```javascript
    <script>
        // DOMContentLoaded：DOM 结构加载完成后触发（无需等图片）
        document.addEventListener('DOMContentLoaded', function () {
            console.log('✅ DOMContentLoaded 触发：DOM 结构已就绪');
            // 此处可安全操作 DOM（如给元素绑定事件、修改文本）
            const container = document.querySelector('.demo-container');
            container.innerHTML += '<p style="color: #2ecc71;">DOM 操作已执行（无需等待图片）</p>';
        });

        // load：整个页面所有资源加载完成后触发（含图片、样式表等）
        window.addEventListener('load', function () {
            console.log('✅ load 触发：所有资源（DOM+图片+样式）已加载完成');
            // 此处可获取图片尺寸等依赖资源的信息
            const demoImage = document.querySelector('.demo-image');
            console.log('图片实际尺寸：', demoImage.offsetWidth + 'px × ' + demoImage.offsetHeight + 'px');
        });
    </script>
```