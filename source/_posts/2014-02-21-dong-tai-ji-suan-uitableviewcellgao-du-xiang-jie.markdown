---
layout: post
title: "动态计算UITableViewCell高度详解"
date: 2014-02-21 21:49:56 +0800
comments: true
categories: [iOS]
---

不知道大家有没有发现，在iOS APP开发过程中，UITableView是我们显示内容常见的控件，本人觉得它是UIKit中最复杂的一个控件。今天要向大家介绍的就是如何动态计算UITableViewCell高度的一经验与技巧，在此做一些总结方便朋友们查阅。<br>

>同时也欢迎广大iOS技术人员加入技术开发群：<疯狂IT人>93916004，众人拾柴火焰高，大家一起讨论研究。<br>

为了不让讲解空洞抽象，我还是用代码实例的方式进行讲解，这样更容易接收与学习。<br>
本文将介绍四种情况下UITableViewCell的计算方式，分别是：<br>

1. [Auto Layout with UILabel in UITableViewCell](#md-1)
2. [Auto Layout with UITextView in UITableViewCell](#md-2)
3. [Manual Layout with UILabel in UITableViewCell](#md-3)
4. [Manual Layout with UITextView in UITableViewCell](#md-4)
5. [随UITextView高度动态改变Cell高度](#md-5)

>由于只是一个demo，所以命名这些都是随意从简。

首先创建一个Single Page的工程，我命名为CellHeightDemo


###<a name="md-1" id="md-1">Auto Layout with UILabel in UITableViewCell</a>
创建一个空的xib,命名为C1.xib, 然后拖入一个UITableViewCell控件。接着创建一个UITableViewCell的子类，命名为C1类。然后在C1.xib中，将与C1类进行关联。别给我说你不会关联，如果不会那看下图你就明白了。^V^<br>
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edryoxqtp4j207806v0sx.jpg)<br>
只需要在Class那里写入关联的类名C1即可。<br><br>

还有由于UITableViewCell需要重用功能，所以我们还需要设置一个重用标识<br>
![](http://ww1.sinaimg.cn/large/6bf526ffgw1edrysb61mgj207906vdg8.jpg)<br>
在Identifier那里写入重用标识C1,当然你也可以用任意字符。不过后面代码里需要这个字符。<br><br>

接着我们来布局。用到了auto layout, 在此我不想介绍auto layout, 以后有时间再专门介绍，下图就是我布局<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1edrywtqnijj20vk0dv416.jpg)<br>
这儿有两点需要说明：1. UILabel的属性Lines这儿设为了0表示显示多行。2. Auto Layout一定要建立完完整。<br><br>

接着我们在UITableView中来使用我们自定义的UITableViewCell C1.<br>
首先我们创建一个UITableViewController的子类T1ViewController, 接着在Main.storyboard中拖入一个UITableViewController，并关联T1ViewController.<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1edrzk4ttqrj20kj0i7dhk.jpg)<br><br>

一切都准备好了，那我们现在来写点代码，给UITableView加点料。<br>
我们想要我们的UITableView使用C1.xib中自定义的Cell,那么我们需要向UITableView进行注册。<br>

```
UINib *cellNib = [UINib nibWithNibName:@"C1" bundle:nil];
[self.tableView registerNib:cellNib forCellReuseIdentifier:@"C1"];
```
这样就进行注册了，接着我们还需要每行显示的数据，为了简单一点，我就声明了一个NSArray变量来存放数据。

```
self.tableData = @[@"1\n2\n3\n4\n5\n6", @"123456789012345678901234567890", @"1\n2", @"1\n2\n3", @"1"];
```

现在实现UITableViewDataSource的protocol:

```
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    // Return the number of rows in the section.
    return self.tableData.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    C1 *cell = [self.tableView dequeueReusableCellWithIdentifier:@"C1"];
    cell.t.text = [self.tableData objectAtIndex:indexPath.row];
    return cell;
}
```
从self.tableData中的数据我们可以看到，每一个Cell显示的数据高度是不一样的，那么我们需要动态计算Cell的高度。由于是auto layout，所以我们需要用到一个新的API systemLayoutSizeFittingSize:来计算UITableViewCell所占空间高度。Cell的高度是在- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath这个UITableViewDelegate的方法里面传给UITableView的。<br>

>这里有一个需要特别注意的问题，也是效率问题。UITableView是一次性计算完所有Cell的高度，如果有1W个Cell，那么- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath就会触发1W次，然后才显示内容。不过在iOS7以后，提供了一个新方法可以避免这1W次调用，它就是- (CGFloat)tableView:(UITableView *)tableView estimatedHeightForRowAtIndexPath:(NSIndexPath *)indexPath。要求返回一个Cell的估计值，实现了这个方法，那只有显示的Cell才会触发计算高度的protocol. 由于systemLayoutSizeFittingSize需要cell的一个实例才能计算，所以这儿用一个成员变量存一个Cell的实列，这样就不需要每次计算Cell高度的时候去动态生成一个Cell实例，这样即方便也高效也少用内存，可谓一举三得。

我们声明一个存计算Cell高度的实例变量：

```
@property (nonatomic, strong) UITableViewCell *prototypeCell;
```

然后初始化它：

```
self.prototypeCell  = [self.tableView dequeueReusableCellWithIdentifier:@"C1"];
```

下面是计算Cell高度的实现：

```
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    C1 *cell = (C1 *)self.prototypeCell;
    cell.t.text = [self.tableData objectAtIndex:indexPath.row];
    CGSize size = [cell.contentView systemLayoutSizeFittingSize:UILayoutFittingCompressedSize];
    NSLog(@"h=%f", size.height + 1);
    return 1  + size.height;
}
```
看了代码，可能你有点疑问，为何这儿要加1呢？笔者告诉你，如果不加1，结果就是错误的，Cell中UILabel将显示不正确。原因就是因为这行代码CGSize size = [cell.contentView systemLayoutSizeFittingSize:UILayoutFittingCompressedSize];由于是在cell.contentView上调用这个方法，那么返回的值将是contentView的高度，UITableViewCell的高度要比它的contentView要高1,也就是它的分隔线的高度。如果你不相信，那请看C1.xib的属性，比较下面两张图。<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1eds2g75es4j20vi0bd3zx.jpg)
![](http://ww1.sinaimg.cn/large/6bf526ffgw1eds2gqoui2j20vl0eegni.jpg)
发现没Cell的高度是127, 面contentView的高度是126, 这下明白了吧。

为了让读者看清楚，我将Cell中UILabel的背景色充为了light gray.下面是运行效果：<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1eds2k4ighsj208y0ejdgc.jpg)


###<a name="md-2" id="md-2">Auto Layout with UITextView in UITableViewCell</a>
本小段教程将介绍UITextView在cell中计算高度需要注意的地方。同样参考上面我们创建一个C2.xib, UITableViewCell的子类C2,并关联C2.xib与C2类。并在C2.xib中对其布局，同样使用了auto layout. 布局如下图：<br>
![](http://ww4.sinaimg.cn/large/6bf526ffgw1eds2o28t18j20vp0ej41f.jpg)

创始UITableViewController的了类T2ViewController，在Main.storyboard中拖入UITableViewController,并关联他们。接着代码中注册C2.xib到UITableView.

下面计是计算高度的代码：

```
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    C2 *cell = (C2 *)self.prototypeCell;
    cell.t.text = [self.tableData objectAtIndex:indexPath.row];
    CGSize size = [cell.contentView systemLayoutSizeFittingSize:UILayoutFittingCompressedSize];
    CGSize textViewSize = [cell.t sizeThatFits:CGSizeMake(cell.t.frame.size.width, FLT_MAX)];
    CGFloat h = size.height + textViewSize.height;
    h = h > 89 ? h : 89;  //89是图片显示的最低高度， 见xib
    NSLog(@"h=%f", h);
    return 1 + h;
}
```

在这儿我们是通过sizeThatFits:计算的UITextView的高度(这是计算UITextView内容全部显示时的方法，在第四小段中我们还会用到它)，然后加上systemLayoutSizeFittingSize:返回的高度。为什么要这样呢？ 因为UITextView内容的高度不会影响systemLayoutSizeFittingSize计算。这句话什么意思呢？我真不知道如何用言语表达了。还是先上一张图吧：<br>
![](http://ww2.sinaimg.cn/large/6bf526ffgw1eds2zaziwfj20bf05udg4.jpg)<br>
此图中距顶的约束是10, 距底的约束8, 距左边约束是87,距右边的约束是13, 那么systemLayoutSizeFittingSize:返回的CGSize为height等于19, size等于100. 它UITextView的frame是不影响systemLayoutSizeFittingSize:的计算。不知道这样说大家明白没。<br>
所以，我们需要加上textViewSize.height. <br>

下面是运行效果：<br>
![](http://ww3.sinaimg.cn/large/6bf526ffgw1eds36rqilej208w0cpt94.jpg)


###<a name="md-3" id="md-3">Manual Layout with UILabel in UITableViewCell</a>
本小段教程将介绍UILabel在Manual layout cell中计算高度， 原理是根据字体与字符串长度来计算长度与宽度。
按照前面介绍的，我们需要创建C3.xib, C3类, T3ViewController类,Main.storyboard中拖入UITableViewController，并分别建立关联。 为了简单，C3.xib中我就不加padding之类的了，如图<br>
>记得关闭C3.xib的auto layout

![](http://ww1.sinaimg.cn/large/6bf526ffgw1eds3ek4ejjj20vq0dr0ue.jpg)<br>

直接上代码了：

```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    C3 *cell = [self.tableView dequeueReusableCellWithIdentifier:@"C3"];
    cell.t.text = [self.tableData objectAtIndex:indexPath.row];
    [cell.t sizeToFit];
    return cell;
}

- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    C3 *cell = (C3 *)self.prototypeCell;
    NSString *str = [self.tableData objectAtIndex:indexPath.row];
    cell.t.text = str;
    CGSize s = [str calculateSize:CGSizeMake(cell.t.frame.size.width, FLT_MAX) font:cell.t.font];
    CGFloat defaultHeight = cell.contentView.frame.size.height;
    CGFloat height = s.height > defaultHeight ? s.height : defaultHeight;
    NSLog(@"h=%f", height);
    return 1  + height;
}
```

这儿用到了一个NSString的Cagetory方法：

```
- (CGSize)calculateSize:(CGSize)size font:(UIFont *)font {
    CGSize expectedLabelSize = CGSizeZero;
    
    if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7) {
        NSMutableParagraphStyle *paragraphStyle = [[NSMutableParagraphStyle alloc] init];
        paragraphStyle.lineBreakMode = NSLineBreakByWordWrapping;
        NSDictionary *attributes = @{NSFontAttributeName:font, NSParagraphStyleAttributeName:paragraphStyle.copy};
        
        expectedLabelSize = [self boundingRectWithSize:size options:NSStringDrawingUsesLineFragmentOrigin attributes:attributes context:nil].size;
    }
    else {
        expectedLabelSize = [self sizeWithFont:font
                                       constrainedToSize:size
                                           lineBreakMode:NSLineBreakByWordWrapping];
    }

    return CGSizeMake(ceil(expectedLabelSize.width), ceil(expectedLabelSize.height));
}
```

原理上面我已说了，这儿没有什么好说明的，代码一目了然。

运行效果如图：<br>
![](http://ww1.sinaimg.cn/large/6bf526ffgw1eds3jmducyj208u0ekjrq.jpg)


###<a name="md-4" id="md-4">Manual Layout with UITextView in UITableViewCell</a>

本小段教程将介绍UITextView在Manual layout cell中计算高度， 原理是与第二小节里的原因，用sizeThatFits:的方法计算UITextView的长度与高度。然后加上padding就是Cell的高度。
按照前面介绍的，我们需要创建C4.xib, C4类, T4ViewController类,Main.storyboard中拖入UITableViewController，并分别建立关联。 为了简单，C4.xib中我就不加padding之类的了，如图<br>
>计得关闭C4.xib的auto layout

![](http://ww3.sinaimg.cn/large/6bf526ffgw1eds3nkgxyyj20vn0g6dhh.jpg) <br>

也直接上代码了，直观明了：<br>

```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    C4 *cell = [self.tableView dequeueReusableCellWithIdentifier:@"C4"];
    cell.t.text = [self.tableData objectAtIndex:indexPath.row];
    [cell.t sizeToFit];
    return cell;
}

- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    C4 *cell = (C4 *)self.prototypeCell;
    NSString *str = [self.tableData objectAtIndex:indexPath.row];
    cell.t.text = str;
    CGSize s =  [cell.t sizeThatFits:CGSizeMake(cell.t.frame.size.width, FLT_MAX)];
    CGFloat defaultHeight = cell.contentView.frame.size.height;
    CGFloat height = s.height > defaultHeight ? s.height : defaultHeight;
    return 1  + height;
}
```

运行效果：<br>
![](http://ww4.sinaimg.cn/large/6bf526ffgw1eds3q86ryyj208y0ei0t5.jpg) <br>

###<a name="md-5" id="md-5">随UITextView高度动态改变Cell高度</a>
本小节要介绍的一个功能是，UITextView中UITableViewCell中，当输入UITextView中的字变多/变少时，高度变化，Cell高度与随之变化的功能。<br>
按照前面介绍的，我们需要创建C5.xib, C5类, T5ViewController类,Main.storyboard中拖入UITableViewController，并分别建立关联。 为了简单，C5.xib中我就不加padding之类的了，如图<br>
>记得开启C5.xib的auto layout

![](http://ww3.sinaimg.cn/large/6bf526ffgw1eds3y3bt19j20vn0fq766.jpg) <br>

先看代码：<br>

```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    C5 *cell = [self.tableView dequeueReusableCellWithIdentifier:@"C5"];
    cell.t.text = @"123";
    cell.t.delegate = self;
    return cell;
}

#pragma mark - UITableViewDelegate
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    C5 *cell = (C5 *)self.prototypeCell;
    cell.t.text = self.updatedStr;
    CGSize s =  [cell.t sizeThatFits:CGSizeMake(cell.t.frame.size.width, FLT_MAX)];
    CGFloat defaultHeight = cell.contentView.frame.size.height;
    CGFloat height = s.height > defaultHeight ? s.height : defaultHeight;
    return 1  + height;
}

#pragma mark - UITextViewDelegate
- (BOOL)textView:(UITextView *)textView shouldChangeTextInRange:(NSRange)range replacementText:(NSString *)text {
    if ([text isEqualToString:@"\n"]) {
        NSLog(@"h=%f", textView.contentSize.height);
    }
    return YES;
}

- (void)textViewDidChange:(UITextView *)textView {
    self.updatedStr = textView.text;
    [self.tableView beginUpdates];
    [self.tableView endUpdates];
}

```

原理就是UITextView内容改变的时候，计算自身高度，然后通知UITableView更新，这样就会触发UITableViewCell高度重新计算，以达到目的。 <br>

---
本文只是简单的介绍了一些原理与技巧，细节之处还请参看[源码](https://github.com/ondev/CellHeightDemo) <br>

>如果你有什么问题，请留言或加入QQ技术群:<疯狂IT人>93916004


参考：<br>
<http://www.howlin-interactive.com/2013/01/creating-a-self-sizing-uitextview-within-a-uitableviewcell-in-ios-6/>
<http://johnszumski.com/blog/auto-layout-for-table-view-cells-with-dynamic-heights>
<http://technet.weblineindia.com/mobile/add-auto-layout-support-for-uiscrollview-with-example-in-ios-app-development/>
<http://useyourloaf.com/blog/2014/02/14/table-view-cells-with-varying-row-heights.html>