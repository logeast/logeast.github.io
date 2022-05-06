---
layout: post
title: 修复 React 和 TypeScript 中 CSS 自定义属性报错问题
date: 2022-04-29
categories: [Thinking, React, TypeScript]
---

## 问题

在 TypeScript 中使用 CSS 自定义属性时，会有这样一个错误提示。

```shell
TS2326: Types of property 'style' are incompatible.
Type '{ "--color": string; }' is not assignable to type 'CSSProperties'.
Object literal may only specify known properties, and '"--color"' does not exist in type 'CSSProperties'.
```

## 解决方案

有两种方法可以修复这个错误。

### 方法 1. 将对象转换为 `React.CSSProperties`

```typescript
const style = {
  "--color": "tomato",
} as React.CSSProperties;
```

### 方法 2. 创建一个继承 `React.CSSProperties` 的接口

```typescript
interface CSSPropertiesWithVars extends CSSProperties {
  "--color": string;
}
```
