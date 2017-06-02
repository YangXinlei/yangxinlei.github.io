---
layout: post
title:  "OC单例的最佳实现到底是怎样的"
date:   2016-09-30 15:49:21 +0800
categories: ios
---

---

### non-ARC

---
由于Apple曾经给出过一段[“标准”实现](http://cocoa.venj.me/blog/singleton-in-objc/)，稍加修改便可转换为*线程安全*的代码，不再讨论。

```objc
#import "MyManager.h"

static MyManager *sharedMyManager = nil;

@implementation MyManager

@synthesize someProperty;

#pragma mark Singleton Methods
+ (id)sharedManager {
  @synchronized(self) {
      if(sharedMyManager == nil)
          sharedMyManager = [[super allocWithZone:NULL] init];
  }
  return sharedMyManager;
}
+ (id)allocWithZone:(NSZone *)zone {
  return [[self sharedManager] retain];
}
- (id)copyWithZone:(NSZone *)zone {
  return self;
}
- (id)retain {
  return self;
}
- (unsigned)retainCount {
  return UINT_MAX; //denotes an object that cannot be released
}
- (oneway void)release {
  // never release
}
- (id)autorelease {
  return self;
}
- (id)init {
  if (self = [super init]) {
      someProperty = [[NSString alloc] initWithString:@"Default Property Value"];
  }
  return self;
}
- (void)dealloc {
  // Should never be called, but just here for clarity really.
  [someProperty release];
  [super dealloc];
}

@end
```
> *code by [Matt Galloway](http://www.galloway.me.uk/tutorials/singleton-classes/)*

值得注意的一点是`alloc`消息会随后走`allocWithZone:`, 就像`copy`会走`copyWithZone:`一样，这种方法实现的单例是可以保证**使用安全**的，即可以保证`[MyManager alloc] init]`，`[manager copy]` 与`[MyManager sharedManager]`得到同一个实例。

---

### ARC

---

尽管查了很多资料，但是`ARC`版本似乎没有类似的保证*使用安全*的方法。

```objc
+ (id)sharedManager {
    static MyManager *sharedMyManager = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedMyManager = [[self alloc] init];
    });
    return sharedMyManager;
}

- (id)init {
  if (self = [super init]) {
      someProperty = [[NSString alloc] initWithString:@"Default Property Value"];
  }
  return self;
}
```

在这种实现方式下，如果想要得到*使用安全*方面的保证（或者*部分保证*），有下述方式可以尝试：

 1.告知编译器不允许调用`init`方法：

```objc
// MyManager.h
- (instancetype)init __attribute__((unavailable("Cannot use init for this class, use +(ArcSingleton*)sharedInstance instead!")));
```
 	
2.如果还觉得不够，想要在运行时也做到调用禁止，可以这样：

```objc

@implementation MyClass

static BOOL useinside = NO;
static id _sharedObject = nil;


+(id) alloc {
		if (!useinside) {
				@throw [NSException exceptionWithName:@"Singleton Vialotaion" reason:@"You are violating the singleton class usage. Please call +sharedInstance method" userInfo:nil];
		}
		else {
				return [super alloc];
		}
}

+(id)sharedInstance
{
		static dispatch_once_t p = 0;
		dispatch_once(&p, ^{
				useinside = YES;
				_sharedObject = [[MyClass alloc] init];
				useinside = NO;
		});   
		// returns the same object each time
		return _sharedObject;
}

```

> *见 [stackoverflow](http://stackoverflow.com/questions/5720029/create-singleton-using-gcds-dispatch-once-in-objective-c/18382143#18382143)*

有两个地方值得注意。

 1.  如果像非ARC那样加上`allocWithZone:`并使用 `[MyManager alloc] init]`初始化，会在`dispatch_once`那里崩掉。
 2.  在`dispatch_once`里面的初始化，无论是向`self`、`[self class]`、`MyManager`发`alloc`消息应该都是一样的。三者都是`MyManager`。
 3.  `NSObject`协议本身不支持`copy`，所以`copy`方法不是必须的。（如果没有，调用(*当然*)会崩溃）