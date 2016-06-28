---
layout: post
title:  "Core IOS Sample 2-3 自定义滑杆图片"
date:   2016-06-28 17:04:33 +0800
categories: ios cookbook
---

![slider](/src/images/cookbook_gif/2-3/2-3.gif)

# 图片缩放

1. 添加目标

{% highlight objc %}
[slider addTarget:self action:@selector(updateValue:) forControlEvents:UIControlEventValueChanged];
{% endhighlight %}

2. 设置动作

{% highlight objc %}
- (void)updateValue:(UISlider *)aSlider
{
    // Scale the title view
    imageView.transform = CGAffineTransformMakeScale(1.0f + 4.0f * aSlider.value, 1.0f);
}
{% endhighlight %}

# 设置滑柄（thumb）图片

{% highlight objc %}
- (void)setThumbImage:(nullable UIImage *)image forState:(UIControlState)state;
{% endhighlight %}

# CGRectInset

{% highlight objc %}
CGRect CGRectInset ( CGRect rect, CGFloat dx, CGFloat dy );
{% endhighlight %}

中心不变，上下左右边界向内分别收缩dx， dy。（二者为负数时则是放大）

# 图形上下文

 ![thumb](/src/images/cookbook_gif/2-3/thumb.png)

如何使用 `Graphics Contexts` 来画一个这样的图形?

{% highlight objc %}
// Create a thumb image using a grayscale/numeric level
UIImage *thumbWithLevel (float aLevel)
{
    float INSET_AMT = 1.5f;
    CGRect baseRect = CGRectMake(0.0f, 0.0f, 40.0f, 100.0f);
    CGRect thumbRect = CGRectMake(0.0f, 40.0f, 40.0f, 20.0f);
    
    UIGraphicsBeginImageContext(baseRect.size); 		  // 1. 创建一个Bitmap上下文，其他上下文还有 PDF、打印机等，上下文不同，绘制的地方就不同。
    CGContextRef context = UIGraphicsGetCurrentContext(); // 2. 获取bitmap上下文
    
    // Create a filled rect for the thumb
    [[UIColor darkGrayColor] setFill];                             // 3. 设置填充色
    CGContextAddRect(context, CGRectInset(thumbRect, INSET_AMT, INSET_AMT));   // 4. 画一个矩形
    CGContextFillPath(context);	                                   // 5. 填充矩形
    
    // Outline the thumb
    [[UIColor whiteColor] setStroke];				// 3. 设置描边色
    CGContextSetLineWidth(context, 2.0f);    		
    CGContextAddRect(context, CGRectInset(thumbRect, 2.0f * INSET_AMT, 2.0f * INSET_AMT)); // 4. 画一个矩形
    CGContextStrokePath(context);					// 5. 对矩形描边
    
    // Create a filled ellipse for the indicator
    CGRect ellipseRect = CGRectMake(0.0f, 0.0f, 40.0f, 40.0f);
    [[UIColor colorWithWhite:aLevel alpha:1.0f] setFill];		// 3. 设置填充色
    CGContextAddEllipseInRect(context, ellipseRect); 			// 4. 画一个椭圆
    CGContextFillPath(context);	         // 5. 填充椭圆
    
    // Label with a number
    NSString *numstring = [NSString stringWithFormat:@"%0.1f", aLevel];
    UIColor *textColor = (aLevel > 0.5f) ? [UIColor blackColor] : [UIColor whiteColor];
    UIFont *font = [UIFont fontWithName:@"Georgia" size:20.0f];
    NSMutableParagraphStyle * style = [[NSMutableParagraphStyle alloc] init];
    style.lineBreakMode = NSLineBreakByCharWrapping;
    style.alignment = NSTextAlignmentCenter;
    NSDictionary * attr = @{NSFontAttributeName:font, NSParagraphStyleAttributeName:style, NSForegroundColorAttributeName:textColor};
    [numstring drawInRect:CGRectInset(ellipseRect, 0.0f, 6.0f) withAttributes:attr]; // 4. 添加文本
    
    // Outline the indicator circle
    [[UIColor grayColor] setStroke];            // 3. 设置描边色
    CGContextSetLineWidth(context, 3.0f);    
    CGContextAddEllipseInRect(context, CGRectInset(ellipseRect, 2.0f, 2.0f));	// 4. 画一个椭圆
    CGContextStrokePath(context);   // 5. 填充椭圆
    
    // Build and return the image
    UIImage *theImage = UIGraphicsGetImageFromCurrentImageContext();	// 6.获取生成的图片
    UIGraphicsEndImageContext();		// 7. 关闭图形上下文
    return theImage;
}
{% endhighlight %}

**参考** ：
[Quartz2D使用][Quartz2D_Usage]

[Quartz2D_Usage]: http://www.cnblogs.com/wendingding/p/3803020.html