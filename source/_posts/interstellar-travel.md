---
title: canvas绘制星际穿越
date: 2025-03-10 15:39:51
updated: 2025-03-13 15:39:51
tags: canvas
categories:
---


### 演示demo

[演示demo](https://bzw875.github.io/interstellarTravel.html)

实现方式
使用canvas绘制，使用requestAnimationFrame实现动画效果
随机生成星星，并设置星星的初始位置、速度、大小和颜色
在动画循环中，更新星星的位置，并绘制星星
在动画循环中，如果星星飞出屏幕边界，则用新星星替换
随机隐藏中间的星星，避免视觉效果混乱

``` css
body, html {
  margin: 0;
  padding: 0;
  overflow: hidden;
  background: black;
}
canvas {
  display: block;
}
```

``` html
<canvas id="canvas"></canvas>
```

``` js
/**
 * 星际航向动画效果
 *
 * @param {Object} option - 配置选项对象
 * @param {number} [option.maxSpeed=5] - 星星的最大速度
 * @param {number} [option.maxSize=2] - 星星的最大尺寸
 * @param {number} [option.maxStars=1000] - 动画中最大星星数量
 * @param {HTMLCanvasElement} option.canvas - 用于绘制动画的 canvas 元素
 */
function interstellarTravel(option) {
  // 解构传入的配置参数并设置默认值
  const { maxSpeed = 5, maxSize = 2, maxStars = 1000, canvas } = option;
  
  // 获取 canvas 的 2D 绘图上下文
  const ctx = canvas.getContext('2d');
  // 设置 canvas 尺寸为当前窗口大小
  let width = canvas.width = window.innerWidth;
  let height = canvas.height = window.innerHeight;

  // 监听窗口尺寸变化，实时调整 canvas 尺寸
  window.addEventListener('resize', () => {
    width = canvas.width = window.innerWidth;
    height = canvas.height = window.innerHeight;
  });

  // 定义可选的星星颜色数组
  const colors = [
    '#999999', '#333333', '#666666', '#fafafa',
    '#ffffff', '#ffffff', '#ffffff', '#ffffff', '#ffffff'
  ];

  /**
   * Star 类表示单个星星，包括位置、速度、大小和颜色
   */
  class Star {
    constructor() {
      // 星星初始位置都在屏幕中心
      this.x = width / 2;
      this.y = height / 2;
      // 随机角度（0 到 2π 弧度）
      this.angle = Math.random() * Math.PI * 2;
      // 随机速度，确保不为0（使用 Math.max 防止速度过小）
      this.speed = Math.max(Math.random() * maxSpeed, 0.01);
      // 随机尺寸，确保最小尺寸不为0
      this.size = Math.max(Math.random() * maxSize, 0.1);
      // 随机选择一种颜色
      this.color = colors[Math.floor(Math.random() * colors.length)];
    }
    
    /**
     * 更新星星位置，依据当前角度和速度
     */
    update() {
      this.x += Math.cos(this.angle) * this.speed;
      this.y += Math.sin(this.angle) * this.speed;
    }
    
    /**
     * 绘制星星
     * 如果星星尺寸较大且位于屏幕中心附近，则跳过绘制，以免视觉效果混乱
     */
    draw() {
      if (this.size > 1 && Math.abs(this.x - width / 2) < 100 && Math.abs(this.y - height / 2) < 100) {
        return;
      }
      ctx.beginPath();
      ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
      ctx.fillStyle = this.color;
      ctx.fill();
    }
  }

  // 用于存放所有星星对象的数组
  const stars = [];
  // 预先更新的次数，让动画启动时看起来星星已经运动了一段时间
  const faseCount = 10000;

  // 根据配置生成初始的星星对象
  for (let i = 0; i < maxStars; i++) {
    stars.push(new Star());
  }

  /**
   * 动画循环函数，不断更新星星状态并重绘画布
   */
  function animate() {
    // 每一帧清空画布（填充背景为黑色）
    ctx.fillStyle = 'black';
    ctx.fillRect(0, 0, width, height);

    // 如果当前星星数量不足，则增加新的星星
    if (stars.length < maxStars) {
      stars.push(new Star());
    }

    // 更新并绘制每一颗星星
    stars.forEach((star, index) => {
      star.update();
      star.draw();
      // 如果星星飞出屏幕边界，则用新星星替换
      if (star.x < 0 || star.x > width || star.y < 0 || star.y > height) {
        stars[index] = new Star();
      }
    });
    // 请求下一帧动画，形成平滑动画效果
    requestAnimationFrame(animate);
  }

  // 预先更新星星位置，模拟星星已有运动轨迹
  for (let i = 0; i < faseCount; i++) {
    stars.forEach((star, index) => {
      star.update();
      if (star.x < 0 || star.x > width || star.y < 0 || star.y > height) {
        stars[index] = new Star();
      }
    });
  }
  
  // 启动动画
  animate();
}

// 初始化星际航向动画，传入页面中 id 为 canvas 的元素
interstellarTravel({
  canvas: document.getElementById('canvas')
});
```
