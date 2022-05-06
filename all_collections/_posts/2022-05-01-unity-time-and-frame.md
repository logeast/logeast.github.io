---
layout: post
title: Unity 时间和帧率
date: 2022-05-01
categories: [Computer, Unity]
---

帧率表示 Unity 每秒渲染的帧数，这取决于计算机的硬件。

这段代码是表示控制角色水平移动。

```csharp
public class Example : MonoBehaviour
{
    void Update()
    {
        Movement();
    }

    void Movement()
    {
        float horizontal = Input.GetAxis("Horizontal");

        Vector2 position = transform.position;
        position.x += horizontal * 0.1f;

        transform.position = position;
    }
}
```

## 以`单位/帧`表示移动速度的问题

在 Unity 中每帧调用一次 Update 函数，假设移动速度为 0.1f 个单位/帧。

- 如果游戏以每秒 60 帧速度运行，那么 1s 内移动的距离为 `0.1f * 60 = 6` 个单位；
- 如果游戏以每秒 10 帧速度运行，那么 1s 内移动的距离为 `0.1f * 10 = 1` 个单位。

若一个玩家计算机比较陈旧，只能以 `10 帧/秒` 的速度运行游戏，而另一个玩家的计算机能以 `60 帧/秒` 的速度运行，那么这两个玩家角色的移动速度就会有很大差异，这样就会使游戏的难易程度提高或降低！

在代码中如此表示：

```csharp
position.x += horizontal * 0.1f;
```

## 以`单位/秒`表示移动速度

要解决此问题，我们需要以`单位/秒`来表示移动速度。

在 Unity 中，可以通过 `Time.deltaTime` 来获取每帧所需的时间。

- 如果游戏以每秒 10 帧速度运行，则每帧耗时 `1 / 10 = 0.1` 秒；
- 如果游戏以每秒 60 帧速度运行，则每帧耗时 `1 / 60 = 0.017` 秒。

为此，我们需要通过将速度乘以 Unity 渲染一帧所需的时间来更改移动速度，此时移动速度将以秒表示，具体表示为 `单位/帧 * 帧/秒 = 单位/秒`

现在，我们再次看看移动的距离：

- 如果游戏以每秒 60 帧速度运行，那么 1s 内移动的距离为 `(0.1f * 1/60) * 60 = 0.1` 个单位；
- 如果游戏以每秒 10 帧速度运行，那么 1s 内移动的距离为 `(0.1f * 1/10) * 10 = 0.1` 个单位。

两者完全一样！现在不管是 60 帧的计算机还是 10 帧的计算机，玩家每秒的移动距离都是 0.1f，这样就完美解决了帧率对游戏速度的影响。

在代码中的表示方式如下。

```chsarp
position.x += horizontal * 0.1f * Time.deltaTime;
```
