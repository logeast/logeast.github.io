---
layout: post
title: 友好的颜色和对比度
date: 2022-03-24
categories: [Computer, Thinking]
---

不同人对颜色对比度的感知不同，全世界大约 3.2 亿人有色觉缺陷。大约每 12 名男性和 200 名女性中就有 1 人患有某种形式的“色盲”，这意味着大约 5% 的用户无法按照你预期的方式体验你的网站。

我就有点好奇，不同视力程度的人看我们的网站的时候，看到的颜色到底是怎么样的呢？

为此，我做了一个实验，用浏览器的开发者工具模拟强制视觉缺陷，分别对视力模糊、红绿蓝色盲和全色盲的效果做了对比，以下是对比效果。

![](/assets/2022/2022-03-25-contrast-compare.png)

## WebAIM 准则

[WebAIM 准则](https://webaim.org/standards/wcag/)将文本和背景颜色的对比度分为三个级别：

AA（最低）对比度为 4.5:1；18pt 或 14pt 加粗对比度可降至 3:1。

- A（3:1）：满足残障人士的基本视觉需求。
- AA（4.5:1）：整体消除了残障人士访问 Web 内容障碍。
- AAA（7:1）：为残障人士提供了对 Web 可访问性的改进和增强。

当两种颜色相似时，对比度低，当两种颜色相异时，对比度高。注意下面显示的对比度的差异。

![](https://web-dev.imgix.net/image/admin/DcYclKelVqhQ2CWlIG97.jpg?auto=format&w=1600)

## 高级感知对比度算法 ([APCA](https://w3c.github.io/silver/guidelines/methods/Method-font-characteristic-contrast.html))

这是一种基于现代色彩感知研究来计算对比度的新方法。与 AA/AAA 准则相比，APCA 更加与上下文相关。

1. 空间属性（字体粗细和文本大小）
2. 文本颜色（文本和背景之间的感知亮度差异）
3. 上下文（环境光、周围环境和文本的预期目的）

![](https://web-dev.imgix.net/image/admin/YhGKRLYvt37j3ldlwiXE.png?auto=format&w=1600)

## 工具

浏览器提供了一些工具来帮助检查页面的对比度。

### 1. 模拟视觉缺陷功能

```
控制台 > 渲染 > 模拟视觉缺陷
DevTools > Rendering > Emulate vision deficiency
```

### 2. 检查文字的对比度

```
控制台 > CSS 概览 > 颜色 > 对比度问题
DevTools > CSS Overview > Colors > Contrast
```

### 3. 快速调整对比度

可以通过浏览器开发者工具快速调整对比度。

![](/assets/2022/0324-contrast.png)

### 4. [WAVE Web 可访问性评估工具](https://wave.webaim.org/report)

在这里输入你的网站，可以获得可访问性的评估结果。

## 参考资料

- [颜色和对比度辅助功能](https://web.dev/color-and-contrast-accessibility/?utm_source=devtools#-2)
