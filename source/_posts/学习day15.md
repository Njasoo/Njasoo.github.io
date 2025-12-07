---
title: 学习day15
date: 2025-11-28 14:20:49
tags: ["game"]
---

# framework

```js
BasketballGame (项目根目录)
│
├─ index.html // 游戏入口，包含 Canvas 和菜单
├─ style.css // 全局样式（也可以用 Tailwind）
├─ assets/ // 图片、音效、精灵图等资源
│ ├─ sprites/
│ ├─ sounds/
│ └─ backgrounds/
├─ js/ // JavaScript 模块
│ ├─ main.js // 游戏入口，初始化 Canvas、游戏循环
│ ├─ gameLoop.js // 核心循环：更新逻辑 + 渲染
│ ├─ input.js // 玩家输入控制（键盘 / 触控）
│ ├─ player.js // 玩家角色逻辑（移动、跳跃、投篮）
│ ├─ ai.js // AI 逻辑（电脑玩家控制）
│ ├─ ball.js // 球运动逻辑（抛物线、碰撞检测）
│ ├─ physics.js // 碰撞检测、投篮判定、边界处理
│ ├─ renderer.js // Canvas 渲染模块（场地、人物、球、UI）
│ └─ ui.js // 菜单、得分板、倒计时、游戏状态管理
└─ config.js // 游戏参数（帧率、速度、投篮概率、AI 难度等）
```

# 图片取色器

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>图片像素取色器</title>
    <style>
      #canvas {
        border: 1px solid #ccc;
        cursor: crosshair;
      }
      #colorBox {
        width: 50px;
        height: 50px;
        display: inline-block;
        vertical-align: middle;
        margin-left: 10px;
        border: 1px solid #000;
      }
    </style>
  </head>
  <body>
    <input type="file" id="upload" accept="image/*" />
    <br /><br />
    <canvas id="canvas" width="500" height="500"></canvas>
    <div id="colorBox"></div>
    <span id="colorText"></span>

    <script>
      const upload = document.getElementById("upload");
      const canvas = document.getElementById("canvas");
      const ctx = canvas.getContext("2d");
      const colorBox = document.getElementById("colorBox");
      const colorText = document.getElementById("colorText");

      upload.addEventListener("change", (e) => {
        // 上传图片就会触发
        const file = e.target.files[0];
        if (!file) return;

        const img = new Image();
        img.onload = () => {
          canvas.width = img.width;
          canvas.height = img.height;
          ctx.drawImage(img, 0, 0);
        };
        img.src = URL.createObjectURL(file);
      });

      canvas.addEventListener("mousemove", (e) => {
        const rect = canvas.getBoundingClientRect(); // 获取canvas在页面当中的位置
        const x = Math.floor(e.clientX - rect.left); // 计算当前鼠标位置相对于canvas的位置
        const y = Math.floor(e.clientY - rect.top);

        const pixel = ctx.getImageData(x, y, 1, 1).data; // (x, y)这个位置只获取1*1像素大小的区域
        const [r, g, b, a] = pixel;
        const rgba = `rgba(${r}, ${g}, ${b}, ${a / 255})`; // a是透明度，这里是把它转换到0~1的区间里面

        colorBox.style.backgroundColor = rgba;
        colorText.textContent = rgba;
      });
    </script>
  </body>
</html>
```
