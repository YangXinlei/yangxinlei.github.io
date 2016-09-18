---
layout: post
title:  "Core IOS Sample 4-3 给UIView添加nameTag"
date:   2016-09-18 18:32:21 +0800
categories: ios cookbook
---

## 目的

为了拓展`tag`的实用性，以实现：“名字为`infoLabel`的那个视图”替代“`tag`为`101`的那个视图”之目的。该拓展与纯数字`tag`相比，清晰直观，还能充当**自文档**。

## 相关技术

**`category`**+**`associated object`**

## 实现

```objc

//UIView-NameExtensions.h
@import UIKit;

@interface UIView (NameExtensions)
- (UIView *) viewNamed: (NSString *) aName;
@property (nonatomic, strong) NSString *nametag;
@end

```

```objc

//UIView-NameExtensions.m
/*
 
 Erica Sadun, http://ericasadun.com
 iOS 7 Cookbook
 Use at your own risk. Do no harm.
 
 */

#import "UIView-NameExtensions.h"
#import <objc/runtime.h>

static const char nametag_key; // Thanks Oliver Drobnik

@implementation UIView (NameExtensions)


- (id) nametag 
{
    return objc_getAssociatedObject(self, (void *) &nametag_key);
}

- (void)setNametag:(NSString *)theNametag
{
    objc_setAssociatedObject(self, (void *) &nametag_key, theNametag, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}

- (UIView *)viewWithNametag:(NSString *)aName
{
    if (!aName) return nil;
    
    // Is this the right view?
    if ([self.nametag isEqualToString:aName])
        return self;
    
    // Recurse depth first on subviews
    for (UIView *subview in self.subviews) 
    {
        UIView *resultView = [subview viewNamed:aName];
        if (resultView) return resultView;
    }
    
    // Not found
    return nil;
}

- (UIView *)viewNamed:(NSString *)aName
{
    if (!aName) return nil;
    return [self viewWithNametag:aName];
}

@end

```

## 使用

```objc

UISwitch *aSwitch = (UISwitch *) [self.view viewNamed:@"topSwitch"];
	
((UILabel *)[self.view viewNamed:@"infoLabel"]).text = [NSString stringWithFormat:@"The switch is %@", aSwitch.isOn ? @"on" : @"off"];

```