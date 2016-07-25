---
layout: post
title:  "UIScrollView:contentSize/contentOffset/contentInset"
date:   2016-07-25 11:38:21 +0800
categories: ios
---

```objc
@property(nonatomic)    CGPoint contentOffset;  // default CGPointZero
@property(nonatomic)    CGSize  contentSize;    // default CGSizeZero
@property(nonatomic)    UIEdgeInsets    contentInset;   // default UIEdgeInsetsZero. add additional scroll area around content
```

## contentSize

指整个`scrollView`的大小,它可能大于`frame`的大小,这也是需要进行`scroll`的原因 :]

注意到默认为`(0,0)`这种情况下等价于将其设置为`frame.size`。

## contentOffset

`frame`左上角坐标相对于整个`scrollView`左上角坐标的便宜量。滚动过程中,该值会不断变化。

## contentInset

在`contentSize`四周加一个边框。

注意,当contentInset不为UIEdgeInsetsZero时,如果不对`frame`的`origin`进行设置,初始情况下的布局可能看起来像这样:

![default](/src/images/20160725/default.png)


附stanford ios课程里的截图:

![contentInset](/src/images/20160725/contentInset.png)

![contentOffset](/src/images/20160725/contentOffset.png)
