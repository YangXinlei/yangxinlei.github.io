---
layout: post
title:  "Core IOS Sample 2-2 Button Zoom动画"
date:   2016-06-28 12:09:21 +0800
categories: ios cookbook
---

![button on/off](/src/images/cookbook_gif/2-2/2-2.gif)

为 `button` 添加了两个事件

{% highlight objc %}
[button addTarget:button action:@selector(zoomButton:) forControlEvents: UIControlEventTouchDown | UIControlEventTouchDragInside | UIControlEventTouchDragEnter];
[button addTarget:button action:@selector(relaxButton:) forControlEvents: UIControlEventTouchDragExit | UIControlEventTouchCancel | UIControlEventTouchDragOutside];
{% endhighlight %}

对应 `selector` 为：

{% highlight objc %}
- (void)zoomButton:(UIButton *)aButton
{
    [UIView animateWithDuration:0.2f
                     animations:^{
                         self.transform = CGAffineTransformMakeScale(1.1f, 1.1f);
                     }];
}

- (void)relaxButton:(UIButton *)aButton
{
    [UIView animateWithDuration:0.2f
                     animations:^{
                         self.transform = CGAffineTransformIdentity;
                     }];
}
{% endhighlight %}