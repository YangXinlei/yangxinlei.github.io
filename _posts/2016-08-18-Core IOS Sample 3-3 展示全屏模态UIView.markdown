---
layout: post
title:  "Core IOS Sample 3-3 展示全屏模态UIView"
date:   2016-08-18 11:36:33 +0800
categories: ios cookbook
---

![slider](/src/images/cookbook_gif/3-3/3-3.gif)

# 获得window

* 弹出的模态view覆盖了整个屏幕,这种UIView必须添加到window中。

{% highlight objc %}
UIWindow *window = self.view.window;
...
// 令overlayView捕获所有触摸事件,从而阻止这些事件到达下方的其他GUI
overlayView.userInteractionEnabled = YES;
...
[window addSubview:overlayView];
{% endhighlight %}

# 弹出数秒后消失

{% highlight objc %}
[self performSelector:@selector(removeOverlay:) withObject:overlayView afterDelay:5.0f];
{% endhighlight %}

即等待5s然后调用:

{% highlight objc %}
[self removeOverlay:overlayView];
{% endhighlight %}

当然,`removeOverlay`方法就很简单了:

{% highlight objc %}
- (void)removeOverlay:(UIView *)overlayView
{
    [overlayView removeFromSuperview];
}
{% endhighlight %}

# 关于userInteractionEnabled

对于`UIView`该值默认为`YES`,在本例中若设置`overlayView`的`userInteractionEnabled`为`NO`效果是这样的:

![bug](/src/images/cookbook_gif/3-3/3-31.gif)