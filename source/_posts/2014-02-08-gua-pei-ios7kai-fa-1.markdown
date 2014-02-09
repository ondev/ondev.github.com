---
layout: post
title: "适配iOS7开发1"
date: 2014-02-08 23:02:24 +0800
comments: true
categories: [iOS7]
---
iOS7可以说是苹果一次革命性的变化，这样做为一个开发者需要做的事也不少。首先苹果的view布局有所变化，现在view默认是全屏模式，而所有的bar(navigation bar, tool bar, search bars 与scope bars都是半透明)，status bar则是全透明。有了这样的改变，写代码的时候就有我们折腾的了。 本文所说的适配是指：用xcode5+iOS7sdk编译的程序能正常的运行在iOS6及以下的设备上。

首先看一个例子，用xcode5创建一个Empty Application的工程， 然后新建一个继承UIViewController的类，并将这个类的一个实例做为window的rootViewController. 现在运行程序，将会看到一个空白页。 现在我们向里面加点东西。 在viewDidLoad中加入以下代码

```
self.view.backgroundColor = [UIColor grayColor];
UIButton *btn = [UIButton buttonWithType:UIButtonTypeRoundedRect];
btn.frame = CGRectMake(0, 0, 100, 44);
btn.layer.cornerRadius = 0;
btn.layer.borderColor = [UIColor blackColor].CGColor;
btn.layer.borderWidth = 1;
[btn setTitle:@"Test" forState:UIControlStateNormal];
[self.view addSubview:btn];
```

现在运行，你将会看到在屏幕最顶处有一个黑色边框的button. 
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcdvo2yn4j20hs0vkgls.jpg)

注意没，是与statusBar重叠了。
下面我们做一些修改，修改AppDelegate中的window的rootViewController值：

```
ViewController *vc = [[ViewController alloc] init];
UINavigationController *nav = [[UINavigationController alloc] initWithRootViewController:vc];
self.window.rootViewController = nav;
```

再运行，程序，你将看到
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edcdwv6tt7j20hs0vkq34.jpg)

我们的button到哪儿去了，其实它是被Navigation Bar档住了。细心的读者可能会问，你刚才不是说了Navigation Bar是半透明的吗？即然是半透明的为何我看不到button呢？ 的确Navigation Bar是半透明的，不过透明度不高，然后看不清后成的button.如果你不相信，你可以再一个黑色的view看看效果。将下面代码放入viewDidLoad

```
UIView *v = [[UIView alloc] initWithFrame:CGRectMake(100, 0, 100, 70)];
v.backgroundColor = [UIColor blackColor];
[self.view addSubview:v];
```

然后运行，看看效果：
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edcdyrmfs4j20hs0vkdg1.jpg)

现在可以清楚的看到Navigation Bar后面的view， 可以看到，UIViewController的view是全屏的坐标系了。<br>
这样与我们iOS6与以前的坐标不一样了，以前是从Navigation Bar底部开始计算y值的，现在却是从Status Bar开始计算y值。<br>
不要着急，苹果公司有解决方案：将下面代码放在viewDidLoad中

```
self.edgesForExtendedLayout = UIRectEdgeNone;
```
再运行，看看效果：
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcebwcwusj20hs0vkmxe.jpg)

现在坐标就是开始从Navigation Bar开始计算了。然而这种情况只适合在Navigation Bar的情况，如果没有Navigation Bar, 坐标仍然是从Status Bar顶部开始计算的。<br>
如果底部有Tab Bar的情况呢，如果要兼容以前的frame设置，那么就需要设置UIViewController的edgesForExtendedLayout为UIRectEdgeNone。<br>

###代码如何适配iOS7

如果我们的代码没有用storyboard, 没有用xib,纯代码的情况，如果要适配iOS7，那么上面只是介绍了有Navigation Bar, Tool Bar的情况，如果只是单纯的View,那么我们的代码运行在iOS7上，会有向上20px偏移，这需要手动修改：

```
if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7) {
        self.view.bounds = CGRectMake(0, -20, self.view.frame.size.width, self.view.frame.size.height );
    }
```

这种方法需要对所有的UIViewController进行设置，比较麻烦，还有一种较为简单的方法，就是设置window，这样可以影响所有的subview, 参考自[stackoverflow](http://stackoverflow.com/questions/18294872/ios-7-status-bar-back-to-ios-6-style/18855464#18855464)

```
if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7) {
        [application setStatusBarStyle:UIStatusBarStyleLightContent];
        self.window.clipsToBounds =YES;
        self.window.frame =  CGRectMake(0,20,self.window.frame.size.width,self.window.frame.size.height-20);
        
        //Added on 19th Sep 2013
        self.window.bounds = CGRectMake(0, 20, self.window.frame.size.width, self.window.frame.size.height);
    }
```

下面是第二方法的运行效果：
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edceixx3bmj20hs0vkaab.jpg)

不过第二种方法有问题，当你Rotate Simulator的时候你将会看到问题，Status Bar位置将不动。所以我不推荐用第二种方法，可以写一个UIViewController的category方法，然后调用方法一中的代码。这样对全工程进行修改以适配iOS7.

###下面介绍一下xib的方法
新建一个UIViewController的子类，叫TestViewController，选中创建xib文件 然后，在在AppDelegate中将window的rootViewController换成我们新建的TestViewController实例，代码如下：

```
TestViewController *vc2 = [[TestViewController alloc] init];
self.window.rootViewController = vc2;
```

用xcode4.6.3+ios6运行程序，
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edceozre4oj20hs0vkjrg.jpg)

现在我们在xib中拖入一个button，frame为(177, 0, 73, 44) 
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edces84tk0j20ny0bhwg9.jpg)
由于iOS7中扁平化设计了，button没有了边框，等一下我们会用xcode5+ios7来运行本程序，为了更清楚的看清frame，在此我将button的边框显示出来，前面代码生成的button也是显示了边框的，我们关系一个UIButton的变量textBtn到代码中,并用代码再生成一个button

```
_testBtn.layer.borderWidth = 1;
UIButton *btn = [UIButton buttonWithType:UIButtonTypeRoundedRect];
btn.frame = CGRectMake(0, 0, 100, 44);
btn.layer.cornerRadius = 0;
btn.layer.borderColor = [UIColor blackColor].CGColor;
btn.layer.borderWidth = 1;
[btn setTitle:@"Test" forState:UIControlStateNormal];
[self.view addSubview:btn];
```

运行程序，效果如下： 
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edcf3ld7s9j20hs0vkjrp.jpg)

关闭xocde4.5与Simulator,然后用xcode5打开用iOS7的效果： 
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcf5nev3lj20hs0vk74i.jpg)

对比可以看出，iOS7是全屏的模式。 这样一来，从iOS6中升级过来的程序，布局就不正确了，有20px的偏移，上面介绍了用代码解决的方法，在这儿我们先用代码的方法来试试，在viewWillAppear中加入以下代码：

```
if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7) {
        self.view.bounds = CGRectMake(0, -20, self.view.frame.size.width, self.view.frame.size.height );
    }
```

再运行程序，效果如下：
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edcf8ajwtmj20hs0vkglu.jpg)

其中代码加入的button，下移了20px, 可是xib中拖入的button还是以status bar顶为y＝0开始计算frame.这可能是由于我们用到了auto layout, 所以xib有自己的frame计算方式. 代码生成的没有用auto layout约束，则受到了上面代码的影响。 我们在xocde5中选择xib，如果是xcode4.6创建的xib, xcode5会提示你升级，我们选择升级，如果你选择了忽略也没关系，可以在右边的open in下拉列表中选择xcode5. xcode5对xib做了很多优化，相比以前的xib，xcode5的xib文件不但小，而且功能更强大。 我们禁用xib的autolayout试试，再运行：
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcf93kryej20hs0vkdg2.jpg)

这下由于没有auto layout的作用，都开始从status bar下面开始计算frame了。

如果你的程序view全是xib，没有用代码生成，那么这儿还有一种解决方法，原因是xib中没有开启autolayout)不用代码控制self.view.bounds,所以我们先将viewWillAppear中的

```
if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7) {
        self.view.bounds = CGRectMake(0, -20, self.view.frame.size.width, self.view.frame.size.height );
    }
```
删掉。

然后在在xcode5中选择我们的xib，并选择view中拖入的button，选择它的Size inspector,如图：
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edcffnqm5uj20vo0cygmt.jpg)

细心的你可能发现了，右边较之前的版本多一些内容。多了一个ios6/ios7 Deltas.它就是用来做iOS6的xib适配到iOS7用的东西。 由于iOS7中，全屏的模式，所以我们需要设置我们的＋20,这样才从status bar底部开始显示。现在我们将我们的button的y值设为20.如图：<br>
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edcfgu9hj7j208909tq3e.jpg)

xcode5+ios7运行效果：
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edcfj5xunjj20hs0vkgls.jpg)

由于它的frame是从20开始的，细心的读者可能会有疑问了，如果我们运行在iOS7以前的设备上，由于frame是从20开始的，不是我们想要的效果。的确是这样的，下面是iOS6上运行的效果：
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edcfll9kvjj20hs0vk74d.jpg)

是从status bar下面20px.
也就是说适配工作不成功。 等等，刚才我们说的ios6/ios7 Deltas,现在是时候出场了。 现在我们在将Deltas中y值设为-20.<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edcfmtk3swj20780a3jrv.jpg)

再运行到iOS6的设备上，效果如下：
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edcfnfg3qmj20hs0vkaaa.jpg)

小结： 从iOS7以前的工程适配到iOS7，且没有用autolayout,<br>
1.在viewDidLoad中加入以下代码完成短配工作

```
if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7) {
        self.view.bounds = CGRectMake(0, -20, self.view.frame.size.width, self.view.frame.size.height );
    }
```

2.xib中用调整frame的y值+20，并设置Delats中y为-20. 如果是代码生成的view,则需要手动处理subview的frame的y值，所以都在原来基础上+20

最后我们要记住一点：iOS7默认是全屏的。 所以得有两套值。
