---
layout: post
title: 源码阅读：classnames
date: 2022-05-03
categories: [Source, JavaScript]
---

## 概述

[JedWatson/classnames](https://github.com/JedWatson/classnames) 是一个简单的 JavaScript 库，用于生成 CSS 类名。

`classNames()` 接收任意数量的参数，并返回一个字符串，包含所有参数值为真的类名。

```javascript
classNames("foo", { bar: true }); // => 'foo bar'
classNames({ "foo-bar": false }); // => ''

// 各种类型的参数
classNames("foo", { bar: true, duck: false }, "baz", { quux: true }); // => 'foo bar baz quux'

// 其他为假的参数将被忽略
classNames(null, false, "bar", undefined, 0, 1, { baz: null }, ""); // => 'bar 1'

// 数组参数将被压平
classNames(["foo", { bar: true }]); // => 'foo bar'
```

## 源码思路

[index.js](https://github.com/JedWatson/classnames/blob/master/index.js)

1. 允许输入任意数量的参数
2. 处理不同的参数类型，并返回字符串
3. 将所有的字符串推入一个数组
4. 将数组中的字符串连接起来返回

### 任意数量的参数问题

`classNames` 采用函数的局部方法 `arguments` 来处理参数。源码遍历 `arguments` 对象，并对其中的值按照不同的类型处理。

> `arguments` 是一个对应函数参数的类数组对象，有 length 属性和索引值，但没有数组的内建方法，比如 `forEach()` 和 `map()`。

```javascript
function classNames() {
  "use strict";
  var len = arguments.length;
  for (var i = 0; i < len; i++) {
    var arg = arguments[i];
    console.log(arg);
  }
}
```

### 处理不同的参数类型

将参数类型分为四类：

- 字符串、数字
- 数组
- 其他对象
- 其他类型

1. 对于字符串和数字类型直接压入数组。

```javascript
var classes = [];
// 取得参数的类型
var argType = typeof arg;

if (argType === "string" || argType === "number") {
  classes.push(arg);
}
```

2. 对于数组类型，递归调用 `classNames()`，并将结果压入数组。

```javascript
if (Array.isArray(arg)) {
  if (arg.length) {
    // 递归调用 classNames 函数
    var inner = classNames.apply(null, arg);
    if (inner) {
      classes.push(inner);
    }
  }
}
```

3. 对于对象类型，遍历对象，将属性值为 `true` 的属性名压入数组。

数组也是一种对象，所以这里还需要通过 `arg.toString === Object.prototype.toString` 进行一层判断，过滤掉数组对象。

> `Object.prototype.hasOwnProperty` 用来判断属性是否在对象中，返回布尔值。

```javascript
var hasOwn = {}.hasOwnProperty;

if (argType === "object") {
  if (arg.toString === Object.prototype.toString) {
    // 遍历参数，将值为 true 的键值对推入数组
    for (var key in arg) {
      if (hasOwn.call(arg, key) && arg[key]) {
        classes.push(key);
      }
    }
  }
}
```

4. 其他类型则转换为字符串，并压入数组。

```javascript
arg.toString();
```

## 技巧拓展

### 类数组转换为数组的几种方法

```javascript
// ⚠️ 对参数使用 slice 会阻止 V8 引擎中的优化
var args = Array.prototype.slice.call(arguments);
var args = [].slice.call(arguments);

var args =
  arguments.length === 1 ? [arguments[0]] : Array.apply(null, arguments);

// ES2015
const args = Array.from(arguments);
const args = [...arguments];
```

### 用剩余参数替换 `arguments` 对象

`arguments` 对象只能在（非箭头）函数内使用，且在非严格模式下有不一样的表现。ES2015 提供了剩余参数，我们可以采用该方法来替代 `arguments` 对象。

```javascript
function classNames(...args) {
  for (let i = 0; i < args.length; i++) {
    const arg = args[i];
    console.log(args);
  }
}
```

剩余参数和 `arguments` 对象有如下区别：

1. 剩余参数只包含没有形参的实参，而 arguments 对象包含了传给函数的所有实参。
2. 剩余参数是真正的 Array 实例，可以在它上面使用数组所有的方法。

### 更优雅的参数处理

[dedupe.js](https://github.com/JedWatson/classnames/blob/master/dedupe.js) 是用来去除重复类名的，其中有一些更优雅的方法来处理参数，可以用来借鉴和学习。这里采用了 `Set` 对象来去除重复值。

下面是解析参数的方法。

```javascript
function parseString(resultSet, str) {
  const SPACE = /\s+/;
  const array = str.split(SPACE);
  const length = array.length;

  for (let i = 0; i < length; i++) {
    resultSet[array[i]] = true;
  }
}

function parseNumber(resultSet, num) {
  resultSet[num] = true;
}

function parseArray(resultSet, array) {
  const length = array.length;
  for (let i = 0; i < length; i++) {
    // 数组需要递归调用 parse 方法
    parse(resultSet, array);
  }
}

const hasOwn = {}.hasOwnProperty;
function parseObject(resultSet, object) {
  if (object.toString === object.prototype.toString) {
    for (let k in object) {
      if (hasOwn.call(object, k)) {
        resultSet[k] = !!object[k];
      } else {
        resultSet[object.toString()] = true;
      }
    }
  }
}
```

下面是解析函数，源码是用 if 语句实现的，我们这改为 switch 语句实现。

```javascript
function parse(resultSet, arg) {
  if (!arg) return;
  const argType = typeof arg;
  switch (argType) {
    case "string":
      parseString(resultSet, arg);
      break;
    case "number":
      parseNumber(resultSet, arg);
      break;
    case "object":
      parseObject(resultSet, arg);
      break;
    case "array":
      parseArray(resultSet, arg);
      break;
    default:
      resultSet[arg] = false;
  }
}
```
