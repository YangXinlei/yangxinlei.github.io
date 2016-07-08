---
layout: post
title:  "Core IOS Sample 2-1 Button的四个状态"
date:   2016-06-27 19:20:21 +0800
categories: ioscookbook
---

![button on/off](/src/images/cookbook_gif/2-1/2-1.gif)

四张图片本身是正圆形状的 ![origin](/src/images/cookbook_gif/2-1/origin.png) ，通过 `UIImage` 的 `resizableImageWithCapInsets` 方法进行拉伸。

{% highlight objc %}
UIEdgeInsets edgeInsets = (UIEdgeInsets){0.0f, 110.0f, 0.0f, 110.0f}; //{top, left , bottom, right}
UIImage *greenImg = [[UIImage imageNamed:@"green.png"] resizableImageWithCapInsets:edgeInsets];
{% endhighlight %}

蝴蝶是一个单独的 `UIImageView` ，可以通过一个图片数组设置成gif动画。 该过程在视图控制器的 `viewDidAppear:` 中进行：

{% highlight objc %}
- (void)viewDidAppear:(BOOL)animated
{
    [super viewDidAppear:animated];

    if (!butterflyView)		// 仅初始化一次
    {
        // Load Butterflies
        NSMutableArray *butterflies = [NSMutableArray array];
        UIImage *image;
        for (int i = 1; i <= 17; i++)
        {
            NSString *butterfly = [NSString stringWithFormat:@"bf_%d.png", i];
            if ((image = [UIImage imageNamed:butterfly]))
                [butterflies addObject:image];
        }
        
        CGSize size = ((UIImage *)[butterflies lastObject]).size;
        
        butterflyView = [[UIImageView alloc] initWithFrame:(CGRect){.size = size}];
        [butterflyView setAnimationImages:butterflies];
        [butterflyView setAnimationDuration:1.2f];
        [butterflyView startAnimating];
        [self.view addSubview:butterflyView];
        [self.view sendSubviewToBack:butterflyView];
    }
}
{% endhighlight %}
