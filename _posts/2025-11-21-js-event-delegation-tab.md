---
layout: post
title: "用事件委托实现高效 Tab 切换组件（电商特价页实战）"
author: Lele
tags: [javascript, 事件委托, dom操作, 电商组件]
excerpt: >
  结合电商特价页场景，用事件委托实现 Tab 切换功能，减少事件监听数量，适配动态元素，提升页面性能。
cover: /assets/images/posts/js-tab-ecommerce-cover.png
---

## 需求说明
实现电商平台「今日特价」模块的 Tab 切换功能：
- 点击「精选」「美食」「百货」等 Tab 标签，标签自动高亮；
- 同步切换对应的商品内容区域；
- 要求代码简洁高效，支持动态新增 Tab 无需额外绑定事件。

## 静态页面结构（HTML）
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>今日特价 - 电商特价页</title>
    <style>
        /* 基础样式（所有px替换为百分比/视口单位） */
        .box {
            margin: 0 auto;
            width: 90vw; /* 占视口宽度90% */
            max-width: 800px; /* 最大宽度限制（避免过大） */
            height: 50vw; /* 基于视口宽度的高度，保持比例 */
            max-height: 400px;
            background-color: rgb(251, 249, 249);
        }

        .nav {
            margin: 0 auto;
            width: 100%; 
            height: 12.5%; 
        }

        .nav h3 {
            margin-left: 3.75%; 
            float: left;
            font-size: 2vw; /* 基于视口宽度的字体大小 */
            margin-top: 1%; /* 微调垂直居中 */
        }

        .nav ul {
            margin-top: 5%; 
            margin-right: 3.75%; 
            float: right;
            width: 50%;
            height: 12.5%; 
            list-style: none;
            padding: 0;
        }

        .nav a {
            text-decoration: none;
            color: black;
            margin-left: 10%; 
            float: left;
            line-height: 87.5%; 
            width: 8.75%; 
            height: 87.5%; 
            font-size: 1.5vw; /* 响应式字体 */
            text-align: center;
        }

        /* 激活状态样式 */
        .nav .active {
            color: red;
        }

        /* 内容区域样式 */
        .banner {
            margin-top: 5%; 
            position: relative;
            width: 100%; 
            height: 125%; 
            max-height: 500px;
        }

        .left {
            position: absolute;
            left: 0;
            width: 25%; 
            height: 66%; 
        }

        .left img {
            width: 100%; 
            height: 60.6%; 
            object-fit: cover; 
        }

        .left p {
            display: block;
            margin-left: 15%; 
            font-size: 1.4vw;
            margin-top: 2%;
            margin-bottom: 2%;
        }

        .left p[color="red"] {
            color: red;
            font-weight: bold;
            font-size: 1.6vw;
        }

        .right {
            position: absolute;
            right: 0;
            padding-left: 3.75%; 
            width: 71.25%; 
            height: 60%; 
        }

        .right .rig {
            display: none;
            width: 100%;
            height: 100%;
        }

        .right .active {
            display: block;
        }

        .right img {
            width: 22.6%; 
            height: 48%; 
            margin-right: 1.89%; 
            object-fit: cover;
        }

        .right .rig span {
            font-size: 1.3vw;
            margin-right: 2%;
        }

        
        @media (max-width: 600px) {
            .nav a {
                font-size: 1.2vw;
                margin-left: 8%;
            }
            .left p, .right span {
                font-size: 1.8vw;
            }
        }
    </style>
</head>
<body>
    <div class="box">
        <!-- Tab 导航区域 -->
        <div class="nav">
            <h3>今日特价</h3>
            <ul>
                <li><a href="javascript:;" class="active" data-id="0">精选</a></li>
                <li><a href="javascript:;" data-id="1">美食</a></li>
                <li><a href="javascript:;" data-id="2">百货</a></li>
                <li><a href="javascript:;" data-id="3">个护</a></li>
                <li><a href="javascript:;" data-id="4">预告</a></li>
            </ul>
        </div>

        <!-- 商品内容区域 -->
        <div class="banner">
            <div class="left">
                <img src="/assets/images/posts/tab-discount.png" alt="美白祛斑产品">
                <span>
                    <p>【10片】美白祛斑</p>
                    <p color="red">9.9元</p>
                    <p>已抢150件</p>
                </span>
            </div>
            <div class="right">
                <div class="rig active">
                    <img src="/assets/images/posts/tab-drink.png" alt="饮料组合"><span>饮料组合￥9.9</span>
                    <img src="/assets/images/posts/tab-drink.png" alt="饮料组合"><span>饮料组合￥8.8</span>
                    <img src="/assets/images/posts/tab-drink.png" alt="饮料组合"><span>饮料组合￥7.7</span>
                    <img src="/assets/images/posts/tab-drink.png" alt="饮料组合"><span>饮料组合￥6.6</span>
                </div>
                <div class="rig">
                    <img src="/assets/images/posts/tab-snack.png" alt="零食套盒"><span>零食套盒￥9.9</span>
                    <img src="/assets/images/posts/tab-snack.png" alt="零食套盒"><span>零食套盒￥8.8</span>
                    <img src="/assets/images/posts/tab-snack.png" alt="零食套盒"><span>零食套盒￥7.7</span>
                    <img src="/assets/images/posts/tab-snack.png" alt="零食套盒"><span>零食套盒￥6.6</span>
                </div>
                <div class="rig">
                    <img src="/assets/images/posts/tab-juice.png" alt="果汁套盒"><span>果汁套盒￥9.9</span>
                    <img src="/assets/images/posts/tab-juice.png" alt="果汁套盒"><span>果汁套盒￥8.8</span>
                    <img src="/assets/images/posts/tab-juice.png" alt="果汁套盒"><span>果汁套盒￥7.7</span>
                    <img src="/assets/images/posts/tab-juice.png" alt="果汁套盒"><span>果汁套盒￥6.6</span>
                </div>
                <div class="rig">
                    <img src="/assets/images/posts/tab-beer.png" alt="啤酒套盒"><span>啤酒套盒￥9.9</span>
                    <img src="/assets/images/posts/tab-beer.png" alt="啤酒套盒"><span>啤酒套盒￥8.8</span>
                    <img src="/assets/images/posts/tab-beer.png" alt="啤酒套盒"><span>啤酒套盒￥7.7</span>
                    <img src="/assets/images/posts/tab-beer.png" alt="啤酒套盒"><span>啤酒套盒￥6.6</span>
                </div>
                <div class="rig">
                    <img src="/assets/images/posts/tab-pen.png" alt="荧光笔"><span>莫兰迪色荧光笔￥9.9</span>
                    <img src="/assets/images/posts/tab-pen.png" alt="荧光笔"><span>莫兰迪色荧光笔￥8.8</span>
                    <img src="/assets/images/posts/tab-pen.png" alt="荧光笔"><span>莫兰迪色荧光笔￥7.7</span>
                    <img src="/assets/images/posts/tab-pen.png" alt="荧光笔"><span>莫兰迪色荧光笔￥6.6</span>
                </div>
            </div>
        </div>
    </div>
## 核心script实现代码
<script>
// 获取 Tab 导航父容器（事件委托目标）
const tabNavParent = document.querySelector('.nav ul');

// 绑定事件到父容器，实现事件委托
tabNavParent.addEventListener('click', function(e) {
    // 1. 判断事件源是否为 Tab 标签（a 标签）
    if (e.target.tagName === 'A') {
        // 阻止 a 标签默认跳转行为
        e.preventDefault();

        // 2. 移除所有 Tab 标签的激活状态
        document.querySelectorAll('.nav a').forEach(tab => {
            tab.classList.remove('active');
        });

        // 3. 给当前点击的标签添加激活状态
        e.target.classList.add('active');

        // 4. 获取当前 Tab 对应的索引（从 data-id 属性读取）
        const tabIndex = +e.target.dataset.id; // + 号将字符串转为数字

        // 5. 移除所有内容区域的激活状态
        document.querySelectorAll('.right .rig').forEach(content => {
            content.classList.remove('active');
        });

        // 6. 激活当前 Tab 对应的内容区域（nth-child 索引从 1 开始，需+1）
        document.querySelector(`.right .rig:nth-child(${tabIndex + 1})`).classList.add('active');
    }
});
</script>