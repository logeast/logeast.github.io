---
layout: post
title: Unity 开发技巧
date: 2022-04-03
categories: Thinking Reading
---

记录 Unity 应用过程中的一些小技巧。

Unity 配置 VSCode： [Unity Development with VS Code](https://code.visualstudio.com/docs/other/unity)

- 配置完成后如果遇到问题，可参考[该视频及评论](https://www.bilibili.com/video/BV19741167zU?from=articleDetail)

## 1. 渲染管线（Render Pipeline, RP）

渲染管线执行一系列操作来获取场景的内容，并将这些内容显示在屏幕上。

- 剔除

  - （[视锥体剔除](https://docs.unity3d.com/Manual/UnderstandingFrustum.html)）

  - （[遮挡剔除](https://docs.unity3d.com/Manual/OcclusionCulling.html)）

- 渲染

- 后期处理

常用类型

- [内置渲染管线](https://docs.unity3d.com/cn/2020.3/Manual/built-in-render-pipeline.html)是 Unity 的默认渲染管线。

- [通用渲染管线 (URP)](https://docs.unity3d.com/cn/2020.3/Manual/universal-render-pipeline.html) 是一种可快速轻松自定义的可编程渲染管线。

- [高清渲染管线 (HDRP)](https://docs.unity3d.com/cn/2020.3/Manual/high-definition-render-pipeline.html) 是在高端平台上创建出色的高保真图形。

### 渲染管线转换器

- 一种实用转换方法 `Edit > Rendering > Materials > Convert Selected Build-in Materials to UGP`

- 需要大量转换的，可以用[渲染管线转换器](https://docs.unity3d.com/Packages/com.unity.render-pipelines.universal@12.1/manual/features/rp-converter.html) `Window > Rendering > Render Pipeline Converter`

## 2. 调整影子渲染

### 调整显示距离

- `Custom URP > Shadows > Max Distence`

- 节省性能

- 10

### 几层几级渲染

- `Custom URP > Shadows > Cascade Count`

- 近距离真实阴影

- 远距离虚的阴影

-

## 3. 设置光照

`Window > Renderring > Lighting`&#x20;

- `Lighting Mode > Baked Indirect` 间接烘焙

- `Lightingmapper > GPU` 节省 CPU 开销

- `Environment Light > Source > Color` 环境光选择为天空盒/颜色 控制亮度

## 4. 摆放场景

- `V + Mouse` 顶点自动吸附

- `Q W E R T` 快速切换工具

- `Shift + Command + F` 自动将选择的物体设置为当前这个角度的坐标

- 插件安装：`Window > Package Manager > Search`

- 注意可以分区域摆放，比如山峰、树林、深渊、乱石堆和平地等

### 设置可见范围

因为场景中元素过多，我们需要设置什么时候可见。

- 一方面符合物理规律

- 一方面减少性能消耗

### Polybrush 工具

[Polybrush](https://docs.unity3d.com/Packages/com.unity.polybrush@1.1/manual/index.html) Unity 的网格绘制、雕刻和地理散射工具。

- Low Poly 风格

- 安装：

- 批量刷环境元素

- ⚠️ 选中后会失去修改焦点

- RGP 需要导入对应的包

### ProBuilder

构建、编辑和纹理自定义几何图形，支持自定义顶点数目。

- 比如需要将场景变大，自定义多个点的细节

- `Shirt + mouse` 指向指定菜单可以查看功能

- `W` 键查看原点坐标位置

- 可切换原点坐标位置

- `Shift + Click` 向上

- `Control + Click` 向下

- 材质

### ProGrids

让场景多一些参考线。

## 5. 智能导航地图烘培

智能导航是为了限制角色的行动范围，比如大树不能被穿越，陡峭的高山不能爬上去，深渊附近不能走过去等。

- 避免对树木、石头等物体穿模或碰撞

<!---->

- 打开位置：`Window > AI > Navagation`

- `Agents` 导航系统（navmash）设置人物基本模型，大小，高度

  - `选中人物 > Add Component > Nav Mash Agent`

  - `Bake` 烘培

  - `Object` 设置当前选中对象的烘培条件

- `Object > Static > Not Workable`

- 对可移动物体的烘培

  - `选中人物 > Add Component > Nav Mash Obstacle`

  - 让不可移动范围跟着物体走

## 6. 鼠标控制人物移动

- `using UnityEngine.Events;` 事件命名空间

  - UnityEvent

  - Navagation

- `[System.Serializable]` 序列化非继承 MonoBehaviour

- 鼠标点击，摄像机发出一条射线

  - 📒 `Camera.ScreenPointToRay` 返回从摄像机通过屏幕点的光线

  - 📒 `Physics.Raycast` 获取射线碰撞信息

- 控制鼠标

  - 是否点击/是否到外面

  - 是否点击地面

## 7. 单例模式控制鼠标移动

鼠标移动是一个可重复使用的脚本，但每次我们都需要指定挂载的角色，单例模式让我们避免多次重复挂载。

### 单例模式

1.  在 Manager 中创建 static 的变量 Instance

2.  在 Awake 函数中判断，Instance 不为空就 Destroy

3.  否则 Instance 为当前指向元素

<!---->

    // Mangager.cs
    public class Manager : MonoBehaviour
    {
        public static Manager Instance;

        void Awake()
        {
            if (Instance == null)
            {
                Instance = this;
            }
        }

        void CustomFunction()
        {
            Debug.Log("CustomFunction");
        }
    }

调用方式

1.  创建并将 Controller 挂载到角色上

2.  在 Controller 中可以直接调用

<!---->

    // Controller.cs
    public class Controller : MonoBehaviour
    {
        void Start()
        {
            Manager.Instance.CustomFunction();
        }
    }

### 添加鼠标点击订阅方法

- MouseManager 中

  - 注册 event OnMouseClicked

  <!---->

  - 在 MouseControl 中统一管理

- PlayerController 中

  - 定义函数 MoveToTarget 函数，注意方法要和 event OnMouseClicked 完全对应

  - 在 Start 函数中执行 MoveToTarget 即可

## 8. 鼠标指针贴图

- 导入 png 到 `ssets/Cursors/...` ，设置 `Texture Type > Cursor`

<!---->

- 📒 `Cursor.SetCursor` 设置鼠标贴图

## 9. 摄像机跟踪

### Cinemachine 智能相机

- 虚拟相机替代默认相机

## 10. 美化场景

### 设置迷雾效果

- `Window > Rendering > Lighting > Environment > Flog`

### 设置 Post Processing

- `Main Camera > Rendering > Post Processing` 启动才能渲染

<!---->

- `Hierarchy > Right Click > Volume > Global Volume`&#x20;

  - `profile > new > ...` 添加特效

## 11. 为角色添加动画

- 新建 Animator，并拖入角色的 Animator 组件中

- 添加 Blend Tree

- 将角色动画拖入 Motion，设置对应的阈值

- 为角色添加动画触发的脚本

## 12. Shader Graph 遮挡剔除

- [ ] 为人物角色[添加遮挡剔除](https://www.bilibili.com/video/BV1i5411P7bd/?spm_id_from=trigger_reload)

让角色在遮挡物后面有一个大致的轮廓。

- 新建 Material

- 新建 Shader

- 添加 Fresnel 效果 光圈替换角色

- color 变量，和 Fresnel 对象叠加

- 添加噪点 Dither

- 设置阈值调整渲染

- 为人物添加 render feature

  - 被遮挡时添加一个

  - 出来后再添加一个

## 13. 添加默认挂载组件

在 class 上面添加一行 `[RequireComponent(typeof(NavMeshAgent))]` 。

---

## 基础知识课程

学习 Unity 游戏开发基础

- Unity 基础知识

- Unity C#脚本基础知识

- Unity 动画基础知识

- Unity Physics 基础知识

- Unity 导航基础知识

- Unity 材质知识

- Unity 光照基础知识

- Unity 音频基础知识

- Unity UI 基础知识

- Unity 游戏玩法基础知识

## 美术课程

通过学习 Maya 和 Zbrush 扩展您的技能

- 游戏角色概念设计基础知识

- 游戏环境概念设计

- 在 Unity 和 Maya 中创建游戏环境

- 使用 Substance Suite 创建游戏环境纹理

- 游戏属性建模基础知识

- 在 Zbrush 中雕刻游戏角色

- 在 Maya 中重塑游戏角色

- Maya 中的游戏角色 UV 贴图

- Substance Painter 中的游戏角色纹理

- 游戏角色骨骼绑定基础知识

- Unity 和 Maya 中的游戏角色动画

## 编程课程

学习如何为重要的游戏功能编程

- 角色控制器

- 敌人 AI

- 角色库存系统

- 角色状态系统

- 战斗系统

- 战利品系统

- 游戏管理器、加载器、游戏循环[\
  ](https://www.youtube.com/embed/Aoc0Udb_w_U?autoplay=1&disablekb=1&iv_load_policy=3&modestbranding=1&showinfo=0&html5=1&enablejsapi=1&rel=0)

## 设计课程

深入学习游戏设计

- 设计课程蓝图

- 游戏设计基础知识

- 专业关卡设计基础知识

- 游戏机制设计基础知识

- 《持剑勇士》游戏系统的原型设计

- Unity 中的迭代关卡设计

- 《持剑勇士》：设计模块组

- 使用 Unity 的 Cinemachine 和 Timeline 设计影片动画

- 《持剑勇士》：后期处理和最终润色
