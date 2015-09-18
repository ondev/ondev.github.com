---
layout: post
title: "iOS开发之xib技巧介绍"
date: 2014-02-22 16:16:29 +0800
comments: true
categories: [iOS,UI]
keywords: iOS,UIKit,Xib使用,Nib,界面开发
description: iOS开发中xib使用技巧
---

>本站QQ技术群:<疯狂IT人>93916004

iOS开发的这些年里，我遇到过这样的一群人，他们认为用代码写UI的人就要NB一点，用xib就是要低一个级别。更有一个高级工程师Level的朋友，聊天当中还特意强调自己全用代码写UI。对此我只能在心里暗笑两下。到底是用xib还是代码来创建UI,这个问题以前也有过很多争论，我只想说一点，各有各的优点。如果能够将两者融合贯通，那将是更有优势。笔者开发过程中，UI能用xib就尽量用xib（能用storyboard就用storyboard, 一个storyboard里最好别装太多的UIViewController，这在结队开发中将不利，我在下一篇教程中介绍如何使用多个storyboard来解决这个问题）.本文主要介绍使用xib的一些技术，即在xib中布局UI,然后xib与code相结合，快速UI开发介绍。本文主要讲解的也就是加载xib的技术。<br>

<!-- more -->

还是老方法，用代码说话，首先创建一个Single Page工程，命名为:LoadNibViewDemo.<br>

###1.直接加载xib中的UIView
创建一个View1.xib, 随便设一个背景色，加一个标识UILabel, 这样好知道是这个view是哪一个view. 你可以在这个view上加作意的subview，我只是说明原理，所以这儿并没有加作何subview. 最终我的View1如下图：<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edsa871blkj20vk0ekdhe.jpg)<br>
由于View1会放到其它View上作为subview，所以这儿size是Freeform, Status Bar是:None。<br><br>

将下面代码放到viewDidLoad中:<br>

```
    //1
    NSArray *views = [[NSBundle mainBundle] loadNibNamed:@"View1" owner:nil options:nil]; //&1
    UIView *v = [views lastObject];
    CGRect r = v.frame;
    r.origin.y += 80;
    v.frame = r;
    [self.view addSubview:v];
```
&1这行代码就是加载View1.xib， 然后将xib中的UIView实保存到views中， 由于xib中我们只拖入了一个view, 所以这儿lastObject就返回这个view的实例，这样便加载了xib中的UIView. 接着将这个UIView addSubview到其它view上，运行效果如图：<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edsai5knbvj208v0fsjre.jpg)<br>

###2.通过Owner建立变量关联<br>

首先我们为ViewController创建一个IBOutlet属性:

```
@property (nonatomic, weak) IBOutlet UIView *referencedView;
```
接着同上面介绍的一样创建一个View2.xib, 如下图：<br>
![](http://ww4.sinaimg.cn/large/6bf526ffgw1edsap4cbeoj20vp0ebwfj.jpg)<br><br>

File's Owner中，我们设为ViewController, 这样我们就可以与实例变量_referencedView建立关联了,如图<br>
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edsaret2huj20kg0aogm7.jpg) <br>

接着在viewDidLoad中，在刚才加入的代码下面添加如下代码：<br>

```
    // 2
    [[NSBundle mainBundle] loadNibNamed:@"View2" owner:self options:nil];
    r = _referencedView.frame;
    r.origin.y = v.frame.size.height + v.frame.origin.y;
    _referencedView.frame = r;
    [self.view addSubview:_referencedView];
```
与//1中的代码有点类似，只不过owner属性为self了。这样一来，loadNibNamed后，就会实例化与之关联的变量_referencedView, 运行程序你将会看到效果：<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edsay5nckmj208w0fst8r.jpg)<br>

###3.Class Owner建立变量关联<br>
其实这个原理与上面2说的一样的，只不过这儿我们特别定义一个class来作为xib的Owner， 要所有需要关系的view都可以声明在这个Owner中，这样方便代码管理与维护。<br>
这儿我们声明一个NSObject的子类FileOwner, 然后再在FileOnwer中声明IBOutLet的关联变量：

```
@property (nonatomic, weak) IBOutlet UIView *view;
```
同理创建一个View3.xib, File's Owner设为FileOwner, 并建立view关联：<br>
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edsb5soi16j20ql0c4t9r.jpg)<br><br>

接着在viewDidLoad结尾处添加以下代码:<br>

```
    // 3
    FileOwner *owner = [FileOwner new];
    [[NSBundle mainBundle] loadNibNamed:@"View3" owner:owner options:nil];
    r = owner.view.frame;
    r.origin.y = _referencedView.frame.origin.y + _referencedView.frame.size.height;
    owner.view.frame = r;
    [self.view addSubview:owner.view];
```

运行效果：<br>
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edsb8f38gxj208x0fsglp.jpg)<br><br>

###4.引入UIView Category<br>

为了代码简单，我们增加一个UIView Category方法

```
+(id)loadFromNibNamed:(NSString*) nibName {
    return [FileOwner viewFromNibNamed:nibName];
}
```

其中FileOwner的class 方法：

```
+(id)viewFromNibNamed:(NSString*) nibName {
    FileOwner *owner = [self new];
    [[NSBundle mainBundle] loadNibNamed:nibName owner:owner options:nil];
    return owner.view;
}
```
这样加载xib的代码就会变得更简单。<br>
同理，我们创建一个View4.xib, File's Owner设为FileOwner, 并建立view关联：<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edsbhtxokzj20vn0dut9n.jpg)<br>

接着在viewDidLoad尾添加代码:

```
    // 4
    UIView *v4 = [UIView loadFromNibNamed:@"View4"];
    r = v4.frame;
    r.origin.y = owner.view.frame.origin.y + owner.view.frame.size.height;
    v4.frame = r;
    [self.view addSubview:v4];
```
运行效果:<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edsehvv6clj208w0frq2z.jpg) <br>

###5.自定义UIView类<br>
在4Category的基础上，我们再引入自定义UIView类，并在xib中与之关联。首先我们创建一个UIView字类UIView5.<br>
接着，我们创建一个View5.xib, File's Owner设为FileOwner, 并建立view关联：<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edsen8i9nvj20vq0ed0u2.jpg)<br>

接着增加一个UIView的Category方法：

```
+(id)loadFromNib {
    return [self loadFromNibNamed:NSStringFromClass(self)];
}
```

在viewDidLoad尾加入代码：

```
    // 5
    View5 *v5 = [View5 loadFromNib];
    r = v5.frame;
    r.origin.y = v4.frame.origin.y + v4.frame.size.height;
    v5.frame = r;
    [self.view addSubview:v5];
```

动行效果：<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edserc400wj208x0fuglv.jpg)<br>

###6.设置Onwer为UIViewController
首先，我们创建一个View6.xib, File's Owner设为UIViewController. 这样UIViewController的view属性关联我们xib中的UIView <br>
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edsezb1w85j20vv0dtgn1.jpg)<br>

接着在viewDidLoad中添加代码:<br>

```
    // 6
    UIView *v6 = [[UIViewController alloc] initWithNibName:@"View6" bundle:nil].view;
    r = v6.frame;
    r.origin.y = v5.frame.origin.y + v5.frame.size.height;
    v6.frame = r;
    [self.view addSubview:v6];
```
动行效果:<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edsf1mhayzj208w0ft74f.jpg)<br>

说了这么多，是时候做一下总结了，其实其本是两个方法，一个是没有File's Onwer直接加载xib中的UIView，二是通过File's Onwer关联变量加载xib中的UIView。 然后就是一些Category提供简单接口而已。大家可以再细细品味一下上面所介绍的内容。<br>

大家可以看我源码中UIView+Ext的Category方法中还提供了一个方法：+ (id)loadFromNibNoOwner;它应是方法5与方法1的组合，在此我就不细说了。 都是由上面两个基本方法演变出来的。<br>

###7.xib link xib
大家有没有想过在xib中link其它xib？ 很可惜苹果不支持这个功能。但是我们可以通过一点技巧实现这个功能。下而我就简单介绍一下<br>
先说一下原理，加载xib的UIView，如果这个UIView是自定义的UIView（即xib中关联了UIView的子类），如下图：<br>
![](http://ww4.sinaimg.cn/large/6bf526ffgw1edsfenvw77j20vo0g40ux.jpg)<br>

那么在加载显示这个view的时候会触发一些方法，如:<br>

```
- (id)initWithCoder:(NSCoder *)aDecoder
- (id)awakeAfterUsingCoder:(NSCoder*)aDecoder
```
我们就在这儿作些文章，在这儿用前面介绍的方法加载想要的的xib中UI实例替换掉原来返回的实例。

首先我写了一个UIView的了类SubView，代码很容易理解:

```
#import "SubView.h"
#include "UIView+Ext.h"

@implementation SubView

- (id)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
        // Initialization code
    }
    return self;
}


- (id) awakeAfterUsingCoder:(NSCoder*)aDecoder {
    BOOL theThingThatGotLoadedWasJustAPlaceholder = ([[self subviews] count] == 0);
    if (theThingThatGotLoadedWasJustAPlaceholder) {
        SubView* theRealThing = [[self class] loadFromNibNoOwner];
		
        // pass properties through
        [self copyUIPropertiesTo:theRealThing];
        
        //auto layout
        self.translatesAutoresizingMaskIntoConstraints = NO;
        theRealThing.translatesAutoresizingMaskIntoConstraints = NO;
		
        return theRealThing;
    }
    return self;
}

-(void) copyUIPropertiesTo:(UIView *)view
{
    // reflection did not work to get those lists, so I hardcoded them
    // any suggestions are welcome here
    
    NSArray *properties =
    [NSArray arrayWithObjects: @"frame",@"bounds", @"center", @"transform", @"contentScaleFactor", @"multipleTouchEnabled", @"exclusiveTouch", @"autoresizesSubviews", @"autoresizingMask", @"clipsToBounds", @"backgroundColor", @"alpha", @"opaque", @"clearsContextBeforeDrawing", @"hidden", @"contentMode", @"contentStretch", nil];
    
    // some getters have 'is' prefix
    NSArray *getters =
    [NSArray arrayWithObjects: @"frame", @"bounds", @"center", @"transform", @"contentScaleFactor", @"isMultipleTouchEnabled", @"isExclusiveTouch", @"autoresizesSubviews", @"autoresizingMask", @"clipsToBounds", @"backgroundColor", @"alpha", @"isOpaque", @"clearsContextBeforeDrawing", @"isHidden", @"contentMode", @"contentStretch", nil];
    
    for (int i=0; i<[properties count]; i++)
    {
        NSString * propertyName = [properties objectAtIndex:i];
        NSString * getter = [getters objectAtIndex:i];
        
        SEL getPropertySelector = NSSelectorFromString(getter);
        
        NSString *setterSelectorName =
        [propertyName stringByReplacingCharactersInRange:NSMakeRange(0,1) withString:[[propertyName substringToIndex:1] capitalizedString]];
        
        setterSelectorName = [NSString stringWithFormat:@"set%@:", setterSelectorName];
        
        SEL setPropertySelector = NSSelectorFromString(setterSelectorName);
        
        if ([self respondsToSelector:getPropertySelector] && [view respondsToSelector:setPropertySelector])
        {
            NSObject * propertyValue = [self valueForKey:propertyName];
            
            [view setValue:propertyValue forKey:propertyName];
        }
    }    
}

@end

```

创建一个EmbeddedView.xib，我们想在其它xib中直接link这个EmbeddedView.xib, 还需要创建一个SubView的了类EmbeddedView.<br>
我的xib信息是这样的:<br>
![](http://ww4.sinaimg.cn/large/6bf526ffgw1edsfrbnwxnj212u0ge771.jpg)<br>

一切就绪后，运行:<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edsfsk2afbj208v0ft0ss.jpg)<br>

xib可以快速布署UI, 可以提高开发速度哦。
随便在此预告一下下一篇教程的内容:多Storyboard协作开发。

终于这个教程写完了，完整的Demo可以在此下载:[LoadNibViewDemo](https://github.com/ondev/LoadNibViewDemo).
写个教程我容易吗？ 还希望大家多多支持，你们的支持将是我源源不断的动力。

---
>时间仓促，难免有不少错误，还往指正。若有问题，请留言或加入QQ技术群:<疯狂IT人>93916004
