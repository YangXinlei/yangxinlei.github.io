---
layout: post
title:  "如何在NSDictionary/NSMutableDictionary中使用自定义类型的键"
date:   2016-06-27 15:49:21 +0800
categories: ios
---

## 背景
写了个记账APP，记录一下平时和她在一起时的花销，想要实现的一个功能是根据新建记录项的时刻从历史记录中自动推荐一个合适的花销项。这样在记录一些平时比较规律的花销时可以很方便。

## 那么问题来了

由于没找到小顶堆相关的类，所以就拿NSMutableDictionary来实现。花销项作为key，value则用来记录同一花销出现的次数。由于我的每一项花销是一个自定义类型，导致NSMutableDictionary认为所有的key都不相同，无法实现功能。

## 解决

首先想到需要自定义判断是否相等的方法，查看NSObject可以发现，该方法确实存在，在我的自定义花销项类型中重写该方法：

```
-(BOOL)isEqual:(id)object;
```

### 然而并没有解决...于是

赶快百度，有幸找到了[这篇博文](http://blog.csdn.net/yang3wei/article/details/7804171)。**原来**NSDictionary判断两个键是否相等一共有两步：
- 首先对比二者的hash，如果不相同则直接返回NO
- 然后才调用isEqual方法进行判断
也就是说，还需要重写

```
-(NSUInteger)hash
```

至于如何计算hash值依然是参考了[上面博文](http://blog.csdn.net/yang3wei/article/details/7804171)：

```
-(NSUInteger)hash
{
    return 0;
}
```

对，直接返回了0。这样就直接通过isEqual来比较啦，毕竟isEqual函数也并不复杂。

## 另外

注意字典的KeyType是id<NSCopying>类型，这意味着自定义类型需要遵循NSCopying协议，并实现

```
- (id)copyWithZone:(NSZone *)zone
```

也行正如上述博文所说，字典大概总是会拷贝一份新的对象作为键吧。就像C++那样。
