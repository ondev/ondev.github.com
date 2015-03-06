---
layout: post
title: "Effective Objective-C 2.0学习笔记"
date: 2015-01-22 10:50:41 +0800
comments: true
categories: 
keywords: 
description: 
---

###Item 1 
熟悉C/C++会提高你ObjC的能力 <br>
###Item 2 
包括头文件要注意，尽量写到.m中，.h中尽量用预声明 <br>
###Item 3
尽量用ObjC的精简语法来创建NSFoundation

```
NSNumber *intNumber = @1;
NSNumber *floatNumber = @2.f;
NSNumber *doubleNumber = @3.14;
NSNumber *boolNumber = @YES;
NSNumber *charNumber = @'a'
NSArray *animals = @[@"cat", @"dog", @"mouse", @"badger"];
NSString *dog = animals[1];
NSDictionary *personData = @{@"firstName": @"Matt", @"lastName": @"Galloway", @"age": @28};
NSString *lastName = personData[@"lastName"];
```

###Item 4 尽量用常量代替宏

```
static const NSTimeInterval kAnimationDuration = 0.3;
```
//代替

```
#define ANIMATION_DURATION 0.3
```
static 是为了防止重名重定义，即本文件可见，其它文件不可见，const是为了防止改变其值

.h中extern 定义全局变量，.m中赋值，这样引用.h后就对其变量可见，达到全局变量的效果

###Item 5
正确使用枚举

```
typedef enum ETest: NSInteger {
	ETest1,
	ETest2
}
```
等同

```
typedef NS_ENUM(NSInteger, ETest) {
    ETest1,
    ETest2
}
```
可以使用or运算的optional

```
typedef NS_OPTIONS(NSInteger, ETest) {
    ETest1 = 1 << 0,
    ETest2 = 1 << 1
}
```

###Item 6
理解属性
@public

@private

@property声明数据封装

@synthesize 自动生成操作成员变量的方法

@dynamic 手动生成操作成员变量的方法

类属性的一些属性

nonatomic, readwrite, readonly, assign, strong, weak, unsafe_unretained, copy, getter, setter

###Item 7
类内部实现中最好直接操作类变量，不要用property的方法。

@dynamic自己实现数据封装，能够实现延迟加载

###Item 8 理解对象相等
比较相等要实现isEqual:与hash方法

两个对象相等那么他们的hash返回的值一定相等，hash返回的值相等，两个对象不一定相等

比较两个对象是否相等，根据需要来比较，并不一定要比较所有的属性

自定义hash方法，一定要快速高效的方法

###Item 9 用类簇隐藏子类的执行细节
类簇可以隐藏开放接口后的实现细节

类簇常用在系统框架中

###Item 10 给存在的实例依附数据

```
void objc_setAssociatedObject(id object, void *key, id value, objc_AssociationPolicy policy);
id objc_getAssociatedObject(id object, void *key);
void objc_removeAssociatedObjects(id object);
```
该方法将两个实例关联起来

内存管理一定要注意

不到万不得已的时候不要用，因为出了问题很难找到原因


###Item 11 理解objc_msgSend
Objc中方法调用叫发消息，消息是由消息接收者，selector(路由)与参数组成。发送一个消息给接收者同步对应调用一个方法。

调用就是消息查询路由，然后找到执行入口，最好执行。


###Item 12 理解消息走向
在Item 11中解释了正确情况下的消息走向，接着讲在非正常情况下(即selector不存在的情况下)消息的走向。

如果发送的消息，实例没有对应的selector，那么就会crash并报unrecognized selector send to的错误。

iOS XMPP Framework就运用了这个技巧实现了多代理类

###Item 13 用方法移置来调试,达到hook效果
Method class_getInstanceMethod(Class aClass, SEL aSelector) 获取方法的method
void method_exchangeImplementations(Method m1, Method m2) 交换两个方法的IMP

```
Method originalMethod = class_getInstanceMethod([NSString class],
@selector(lowercaseString));
Method swappedMethod = class_getInstanceMethod([NSString class],
@selector(uppercaseString)); method_exchangeImplementations(originalMethod, swappedMethod);
NSString *string = @"ThIs iS tHe StRiNg";
NSString *lowercaseString = [string lowercaseString]; NSLog(@"lowercaseString = %@", lowercaseString);
// Output: lowercaseString = THIS IS THE STRING
NSString *uppercaseString = [string uppercaseString]; NSLog(@"uppercaseString = %@", uppercaseString);
// Output: uppercaseString = this is the string
```
hook系统方法

```
@interface NSString (EOCMyAdditions) 
- (NSString*)eoc_myLowercaseString; 
@end

@implementation NSString (EOCMyAdditions)
- (NSString*)eoc_myLowercaseString {
NSString *lowercase = [self eoc_myLowercaseString]; 
NSLog(@"%@ => %@", self, lowercase);
return lowercase;
} 
@end

Method originalMethod = class_getInstanceMethod([NSString class], @selector(lowercaseString));
Method swappedMethod = class_getInstanceMethod([NSString class], @selector(eoc_myLowercaseString)); 
method_exchangeImplementations(originalMethod, swappedMethod);

NSString *string = @"ThIs iS tHe StRiNg";
NSString *lowercaseString = [string lowercaseString]; 
// Output: ThIs iS tHe StRiNg => this is the string
```
路由后的执行入口可以被添加，与可以被改变

Swizzling可以用来丰富原来接口的内容

尽量少用，用来调试不错。

### Item 14 理解类对象 
略

### Item 15 类名前缀，防止重复定义
略

### Item 16 初始化方法设计
根据需要设计实现初始化方法

子类中根据需要重写super初始化方法

子类中不需要的父类的初始化方法重写，并在实现中抛出异常

### Item 17 重写description方法
debugDescription或description

NSLog出来更有可读性，用NSDictionary来format

### Item 18 少用可变类型的属性
属性对外只读，对内可读写，实现方式是在.h中readonly, 在.m中用readwrite重新声明一下属性

提供方法来操作可变容器，不要直接声明为属性

### Item 19 命名意图清晰
命名要更像objc

命名要简明，优雅得像一句陈诉句

命名避免缩写

命名风格要一致


### Item 20 私有方法用前缀表明
前缀私有方法用于区别公有方法

不要用_来做私有方法前缀，因为苹果用了这规则


### Item 21 理解Objc Error Model
异常是用来处理fatal error的

非fatal error,要用代理或参数方式来自行处理

### Item 22 理解NSCopying协议
如果想copy，那就一定要执行NSCopying协议

如果有mutable的变量，那么还需要执行NSMutableCopying

区分Shallow copy与Deep copy

建意使用deep copy, 如果需要的话

### Item 23 内部通信使用代理协议与数据协议
delegate在开发中运用得非常多，这儿不再多说

### Item 24 用Category来拆分类，使其更容易维护
很容易明白，也不说了

### Item 25 Category中要加前缀，防止出现bug,容易调试
Category中的类要加前缀

category中方法名要加前缀

### Item 26 Category中避免声明属性
属性是数据的封装，不能在category中声明属性，编译器会找不到的。

有两种方法可以，一种是在Item 10中介绍的，一种是可以声明readonly的属性，然后手动实现其get方法。

### Item 27 用class-continuation的Category隐藏类的执行(类似private方法)
class-continuation category与一般的category差不多，只是括号时用写名字。

class-continuation category可以在里面声明属性与方法，也可以遵循protocol，其中的方法相当于私有方法。

可以改变类属性的一些类型，如只读变成可读写


### Item 28 用protocol提供匿名对象
id加protocol提供匿名类型

如果想隐藏类则可以使用匿名对象

只关心实现了方法，不关心实现细节的时候可以用匿名对象


##5 内存管理
### Item 29 理解引用计数
每个对象使用的时候会对其引用次数进行计数。

new出来的对象retainCount为1.

retain对象retainCount加1.

release对象retainCount减1.

autorelease对象retainCount减1.

当retainCount为0的时候，内存被回收。

对象加到容器(如NSArray)中，会对其retain一次，及引用计数加1,记得要平衡retainCount.

避免循环引用

### Item 30 使用ARC管理引用计数
ARC中将不再手动调用retain,release,autorelease，dealloc等方法了。编译器会自动将这些加入到我们的程序当中。

ARC解放了开发人员对内存的管理，还少写代码。

ARC只能管理ObjC的对象，CoreFoundation object还是得用CFRetain/CFRelease来管理内存

### Item 31 清理与删观察者只放在dealloc中执行
有dealloc中释放CoreFoundation并去掉观察者
```
- (void)dealloc {
    CFRelease(coreFoundationObject);
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}
```
如果是MRC, 调用[super dealloc];应放到最后

如果有文件操作，一定要记得close

### Item 32 内存相关记得异常处理
MRC一定要处理异常处理，方式要正确，@try中需要将释放的工作放到@finally中

ARC要使用-fobjc-arc-exceptions来开启异常处理，不过使用异常处理会降低运行效率

如果有很多异常处理，建意使用NSError的方式来处理

### Item 33 使用弱引用避免循环引用
循环引用就是A对象拥有B对象，B对象拥有A对象，当A想释放的时候，等待B释放，这时B却又要等待A释放完才释放完才释放，这样两个对象相互等待，却一直释放不了。

用weak或unsafe_unretained声明属性就可以了。

weak比unsafe_unretained更安全

### Item 34 使用Autorelease Pool来管理地高内存分配

```
NSArray *databaseRecords = /* ... */;
NSMutableArray *people = [NSMutableArray new]; for (NSDictionary *record in databaseRecords) {
@autoreleasepool { 
EOCPerson *person =
[[EOCPerson alloc] initWithRecord:record];
[people addObject:person];
} 
}
```

如上面的代码，如果NSArray有10000个对象，那么就可以加一个autoreleasepool，这样可以实时的进行内存回收。


### Item 35 使用Zombies来调试内存问题

内存分配给对象，然后对象被释放。如果该对象内存没有被重写，那么再次向该对象发送消息，也是可以成功执行的。如果被重写了，那么就会崩溃。

所以会出现有时候crash,有时候正常运行，给程序调试与bug排除带来不便。所以我们可以开启Zombies（僵尸对象）。

它将所有释放了的对象放到放到一个Zombies区，内存也不释放也不被重写，这样标记它是一个僵尸对象。如果程序再次向僵尸对象发送消息，那么就会在控制台打出消息提供开发人员，方向开发人员定位排除bug.


### Item 36 避免使用retainCount

如果用MRC,开发过程中，中心有retainCount的概念平衡retainCount就可以。代码中一定不要用retainCount来做判断，因为是根据runtime autorelease等决定的。所以release一个对象retainCount不是马上改变。如下面的代码就是错误的，是一个死循环。

```
while([obj retainCount]) {
    [obj release];
}
```

## 6 块与GCD
### Item 37 理解块代码
匿名block

```
^{
    //Block代码
}
```

声明block

```
void (^someBlock)() = ^ {
    //Block代码
}
```

Block声明后，作用域以外的地方是不可见的。如以下代码:

```
void (^block)();
if (/*condition*/) {
    block = ^{
        NSLog("Test block A");
    }
} else {
    block = ^{
        NSLog("Test block B");
    }
}

block();
```

以上代码是有问题的，因为是block的内存是在栈上分配的，出了作用域编译器就会重写栈，所有可能会crash.解决方法是加一个copy， 将内存放到堆上去。

```
void (^block)();
if (/*condition*/) {
    block = [^{
        NSLog("Test block A");
    } copy]
} else {
    block = [^{
    NSLog("Test block B");
    } copy]
}

block();
```
Block内存管理与一般对象一样的。

### Item 38 用typedef定义Block类型
声明block的格式如下

```
type (^blockName)(parameters);
```

使用typedef可以让Block与一般类型一样，使用非常方便。

```
typedef void(^EOCCompletionHandler)(NSData *data, NSError *error);

- (void)startWithCompletionHandler:(EOCCompletionHandler)completion;
```

可见使用typedef让block的使用更加简单


### Item 39 使用Block可以减少代码分很开的弊端
回调方法以前都用Delegate, 可以试试用Block的方式。 用过的人都知道。


### Item 40 使用Block的时候注意循环引用
特别注意Block引用self, 可能会循环引用


### Item 41 Dispatch Queues异步锁
有三种方式使共享资源多线程安全:<br>
#### 使用 @synchronized

```
- (void)synchronizedMethod { 
	@synchronized(self) {		// Safe	} }
```
#### 使用 NSLock

```
_lock = [[NSLock alloc] init];
- (void)synchronizedMethod { 
	[_lock lock];	// Safe	[_lock unlock]; }
```
#### 使用 Dispatch Queues

```
_syncQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);- (NSString*)someString {	__block NSString *localSomeString; dispatch_sync(_syncQueue, ^{	localSomeString = _someString; });	return localSomeString; 
}
- (void)setSomeString:(NSString*)someString { 	dispatch_barrier_async(_syncQueue, ^{	_someString = someString; });￼}
```

### 42 GCD方式的performSelector
延迟执行一般方式

```
[self performSelector:@selector(doSomething) withObject:nilafterDelay:5.0];
```

GCD方式

```
dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5.0 * NSEC_PER_SEC));dispatch_after(time, dispatch_get_main_queue(), ^(void){ 	[self doSomething];});
```

主线程执行一般方式

```
[self performSelectorOnMainThread:@selector(doSomething) withObject:nil waitUntilDone:NO];
```
GCD方式

```
dispatch_async(dispatch_get_main_queue(), ^{	[self doSomething]; 
});
```
ARC方式下的performSelector非常危险，因为是运行时决定的，所以ARC不能在编译的时候对内存进行管理。返回值与参数都有局限。


### 43 什么时候用GCD，什么时候用Operation Queues

GCD是纯C的API, Operation Queues是Objective C. Operation Queues也有像GCD方式的调用，如NSBlockOperation或NSOperationQueue的addOperationWithBlock， Operation Queues还有以下优点：

#### 取消队列
NSOperation的cancel方法可以设置一个取消标志，告诉NSOperation不再运行。而GCD一但运行，就没法取消。

#### 队列依赖
一个消息队列可以依赖其它消息队列，比如下载服务器文件与处理文件两个队列，处理队列依赖下载队列，因为只有下载完后，才能处理文件。

#### 队列的属性可以使用KVO
可以用KVO的方式检测队列的状态

#### 队列优先级
可以用Queues中的每一个NSOperation设置一个优先级，GCD只能对整个队列设置优先级，不但单独对每一个block设置优先级。Operations也可以设置在线程上运行的优先级，同时GCD也可以。

#### 队列重用
Operation的类可以直接重用

GCD并不是唯一的多线程与任务管理方案。Operation提供灵活的方式来实现与GCD相关的功能。

### 44 

待续

























>时间仓促，难免有不少错误，还往指正。