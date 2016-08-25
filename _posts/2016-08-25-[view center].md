---
layout: post
title:  "[view center]"
date:   2016-08-25 18:03:21 +0800
categories: ios
---

`UIView`的`center`属性是该`view`相对其父视图的中心位置
假如有`blueView`和其子视图`redView`

```objc
redView.center = blueView.center;
```

结果是这样的                                | | |  想要下面这种,有两种方法
:-------------------------------------------| | |------------------------------:
![wrong](/src/images/20160825/wrong.png)    | | |  ![right](/src/images/20160825/right.png)

* 将父视图坐标转换到当前视图

```objc
redView.center = [blueView convertPoint:blueView.center fromView:blueView.superview];
```

* 使用`blueView`的`bounds`计算中心点坐标

```objc
redView.center = CGPointMake(CGRectGetMidX(blueView.bounds), CGRectGetMidY(blueView.bounds));
```