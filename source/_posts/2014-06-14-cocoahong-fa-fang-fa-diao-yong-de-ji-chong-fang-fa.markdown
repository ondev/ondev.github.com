---
layout: post
title: "Cocoa触发方法调用的几种方法"
date: 2014-06-14 09:38:40 +0800
comments: true
categories: [iOS,Cocoa]
keywords: [cocoa, methord, ios, 方法调用]
description: 
---
>本站QQ技术群:<疯狂IT人>93916004

今天周未，有点空闲时间，小结一下Cocoa中触发方法调用的几种方法。

###1.SEL触发

SEL就是selector的缩写，它表示Cocoa中的方法选择器，不明白？那请仔细了解Objective_C的运行时机制与Cocoa底层思想。 <br>

```
SEL theSelector = @selector(methodWithInt:andInt:);
```
看了上面代码这下你明白了什么是SEL了吧，平时我们开发当中经常用到的。<br>
有了SEL这样就可以触发方法调用了,<br>

```
[self performSelector:theSelector]
[self performSelector:@selector(methodWithInt:andInt:)];
```
上面两句代码是同一个道理。<br>

###2. IMP触发

IMP其实就是一个函数指针的概念，就可以这么简单理解。<br>

```
IMP theImplementation = [self methodForSelector:theSelector]; 
```
上面这句代码就是获取methodWithInt:andInt:这个方法的地址。<br>
有了这个函数指针后，我们就可以触发方法：

```
theImplementation(self, theSelector, 30, 5);
```
第一个是对象，第二个SEL， 后面的传入的参数。

###3. objc_msgSend方法

这是Objc运行时的一个C方法，我们先看一个示列:<br>

```
objc_msgSend(self, @selector(fly)); 
```
这行代码等价于<br>

[self fly];

###4. NSInvocation

NSInvocation相比上面几种方法，可以动态决定传入的参数个数。有了它，我们就可以实现...这样的变参API封装。<br>
说得有点抽像，看代码更清楚:<br>

假如我们实现了一个两个数相加的方法，

```
-(void)addNSNumber:(NSNumber *)first withNumber:(NSNumber *)second
```
我们可以用下面的方法来触发这个方法，并获取返回值。<BR>

```
- (NSNumber *)testAdd {
	NSNumber *retval;
 
	NSNumber *arg1 = [NSNumber numberWithDouble:15.0];
	NSNumber *arg2 = [NSNumber numberWithDouble:13.0];
 
	SEL selector = @selector(addNSNumber:withNumber:);
 
	NSMethodSignature *sig = [self  methodSignatureForSelector:selector];
	NSInvocation *invocation = [NSInvocation invocationWithMethodSignature:sig];
 
	[invocation setTarget:self];
	[invocation setSelector:selector];
	[invocation setArgument:(void *)&arg1 atIndex:2];
	[invocation setArgument:(void *)&arg2 atIndex:3];
	[invocation invoke];
 
 	if ([sig methodReturnLength]) {
		[invocation getReturnValue:&retval];
		return retval;
	}
	
	return nil;
}
```
代码很容易理解，我也不多做解释，有不明白的请留言。<br>

下面是变参封装, 写了两个NSObject的Extension（Category）:<br>

```
+ (NSInvocation *)createInvocationOnTarget:(id)target selector:(SEL)selector {
	return [NSObject createInvocationOnTarget:target selector:selector withArguments:nil];
}
 
+ (NSInvocation *)createInvocationOnTarget:(id)target selector:(SEL)selector withArguments:(id)arg1, ... {
	NSMethodSignature *sig = [target methodSignatureForSelector:selector];
	NSInvocation *invocation = [NSInvocation invocationWithMethodSignature:sig];
 
	[invocation setTarget:target];
	[invocation setSelector:selector];
 
 
 
	if(arg1) {
		va_list args;
		va_start(args, arg1);
 
		[invocation setArgument:(void *)&arg1 atIndex:2];
 
		id obj;
		int ct = 3;
 
		while( obj = va_arg(args, id) ) {
			NSLog(@"%@", obj);
			[invocation setArgument:(void *)&obj atIndex:ct];
 
			ct++;
		}
 
		va_end(args);
	}
 
 
	return invocation;
}
```

调用方法:

```
NSInvocation *invocation = [NSObject createInvocationOnTarget:mathInstance selector:selector withArguments: arg1, arg2, nil]; 
 
[invocation invoke];
[invocation getReturnValue:&retval];
 
return retval;
```

目前只了解这些方法，如果你有更多的方法，还望提醒<br>

>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004


