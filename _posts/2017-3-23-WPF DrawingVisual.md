---
layout: post
title:  WPF DrawingVisual 
category: wpf 
---

本文：如何自定义控件用 DrawingVisual 画图

本文不会讲 DrawingVisual 是什么，只会告诉简单方法画图。

为何需要学这个，如果需要画出图形，对性能有要求，了解WPF如何画图，就需要知道这个。

先创建最简单使用，就是显示文字或显示点。

我觉得显示文字简单，于是开始写代码，先不要去想做什么

首先新建一个控件，他是可以让 DrawingVisual 显示。


```csharp
    public class MyVisualHost : FrameworkElement
```

这是很基础一个类，几乎没有什么功能。

于是新建一个  FrameworkElement  需要添加 一些方法

![](http://7xqpl8.com1.z0.glb.clouddn.com/AwCCAwMAItoFAMV%2BBQA28wYAAQAEAK4%2BAQBmQwIAaOgJAOjZ%2F2017323102952.jpg)

这个类不是主要的，他是让DrawingVisual显示，在构造函数写

![](http://7xqpl8.com1.z0.glb.clouddn.com/AwCCAwMAItoFAMV%2BBQA28wYAAQAEAK4%2BAQBmQwIAaOgJAOjZ%2F2017323103719.jpg)


这就是可以让 他可以显示。为何这样可以，参见：http://blog.csdn.net/changtianshuiyue/article/details/26981797

主要的类StrokeVisual，其实很简单


```csharp
        public class StrokeVisual : DrawingVisual

```
来看下他的方法 

![](http://7xqpl8.com1.z0.glb.clouddn.com/AwCCAwMAItoFAMV%2BBQA28wYAAQAEAK4%2BAQBmQwIAaOgJAOjZ%2F201732310406.jpg)

这样就可以画出文字。

需要在xaml添加


```csharp
            <local:MyVisualHost></local:MyVisualHost>

```


![](http://7xqpl8.com1.z0.glb.clouddn.com/AwCCAwMAItoFAMV%2BBQA28wYAAQAEAK4%2BAQBmQwIAaOgJAOjZ%2F201732310419.jpg)

为什么这样就可以画出？

那么如何做一个鼠标点下就画点的软件？

调用 RenderOpen 就可以打开一个 DrawingContext ，他提供很多方法，在他上面使用就可以画出，不过画出来看不到。需要添加到FrameworkElement才可以。

那么如何做出下图的程序？

![](http://7xqpl8.com1.z0.glb.clouddn.com/AwCCAwMAItoFAMV%2BBQA28wYAAQAEAK4%2BAQBmQwIAaOgJAOjZ%2F2017%25E5%25B9%25B43%25E6%259C%258823%25E6%2597%25A5%2520112045.gif)

首先对代码做修改，在 Windows 的MouseMove 调用 StrokeVisual 的 Add 方法和 画出来

需要获得鼠标的位置


```csharp
    p=e.GetPosition(this);
```

传入 StrokeVisual 


```csharp
            _s.Add(new StylusPoint(p.X, p.Y));
            _s.Draw();
```


```csharp
        public StrokeVisual()
        {
            Stroke = new Stroke(new StylusPointCollection(new Point[] { new Point(10, 10), }), new DrawingAttributes()
            {
            });


        }

        public void Add(StylusPoint point)
        {
            Stroke.StylusPoints.Add(point);
        }

        private Stroke Stroke;
```
那么如何从 Stroke 画出？

可以使用


```csharp
            using (var dc = RenderOpen())
            {
                Stroke.Draw(dc);
            }
```

Stroke 传入 dc 就可以画出来。

 
