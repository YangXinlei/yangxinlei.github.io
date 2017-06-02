---
layout: post
title:  "如何hook delegate"
date:   2017-06-02 16:20:02 +0800
categories: ios
---


有时，我们想要hook某个delegate的某个方法，做一些神奇的事情。

然而，这个delegate对象是哪个类的实例呢？编码阶段并不总是容易确定。

所以，该如何hook 它的方法呢？

答案就是 `setDelegate`

以`UITableView`为例，下面来hook一下其`delegate`的`tableView:didSelectRowAtIndexPath:`

```objc
@implementation UITableView (swizz)

- (void)swizz_setDelegate:(id)delegate;
{
    
    if ([delegate respondsToSelector:@selector(tableView:didSelectRowAtIndexPath:)])
    {
        [Swizzler swizzInstanceMethodOfClass:[delegate class] originMethod:@selector(tableView:didSelectRowAtIndexPath:) newMethod:@selector(swizz_tableView:didSelectRowAtIndexPath:)];
    }
    
    [self swizz_setDelegate:delegate];
}

@end
```

没错，拿到delegate的class，并施展swizzle大法。无需关心具体是那个类。

剩下的唯一问题是：怎么给这个类添加上`swizz_tableView:didSelectRowAtIndexPath:`方法呢？
我想到的一个简单的解决办法是把这个方法直接添加到`NSObject`里。我们可以使用一个**分类**来做这件事情。

```objc
@implementation NSObject (swizz)

- (void)swizz_tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath
{
    // do something
    
    [self swizz_tableView:tableView didSelectRowAtIndexPath:indexPath];
}

@end
```
【完】