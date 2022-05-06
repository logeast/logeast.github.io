---
layout: post
title: C# 字符串的一些技巧
date: 2022-05-06
categories: [Computer, C#]
---

## 字符串插值工作原理

在 C# 中，字符串插值是通过在字符串前添加 `$`，并在字符串中用 `{}` 嵌入表达式实现的。

字符串插值是调用 `string.Format()` 的语法糖。例如以下语句：

```csharp
System.Console.WriteLine($"Hello, {firstName} {lastName}!");
```

会被转换成以下形式的 C# 语句：

```csharp
object args = new object[] { firstName, lastName };
Console.WriteLine($"Hello, {0} {1}!", args);
```

## 静态方法

这意味着为了调用方法，需要在方法名前附加方法所在类型的名称。

## 实例方法

实例方法不以类型名称开头，而是以变量名作为前缀。

## 字符串属性

Length 是 string 的属性，C# 语法允许像访问成员变量那样访问属性，换言之，属性定义了 setter 和 getter 的特殊方法，使用字段语法可以访问这些方法。

```csharp
string text = "Hello, world!";
Console.WriteLine(text.Length);
```

## 字符串不可变

string 类型的一个关键特征是它不可变（immutable）。所以不可以在一个内存位置将字符串的字母全部转换为大写。

```csharp
string text = "Hello, world!";
text.ToUpper(); // 发生错误！

// 采用下面的方式，就可以避免报错
upperText = text.ToUpper();
```

如有大量字符串需要修改，可以考虑使用 `System.Text.StringBuilder` 类。该类中的 `Append()`、`Insert()`、`Remove()` 等方法可以修改 `StringBuilder` 本身的内容，而不是返回一个新的字符串。
