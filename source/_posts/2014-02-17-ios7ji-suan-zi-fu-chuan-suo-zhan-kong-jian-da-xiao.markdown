---
layout: post
title: "iOS7计算字符串所占空间大小"
date: 2014-02-17 21:42:20 +0800
comments: true
categories: [iOS7]
---

iOS7以前计算NSString所占size的API是NSString的成员方法

```
- (CGSize)sizeWithFont:(UIFont *)font constrainedToSize:(CGSize)size lineBreakMode:(NSLineBreakMode)lineBreakMode
```

现在iOS7中如果用这个方法计算，计算出来的值要比实际值小，所以可能出现显示不完全的效果。这是因为在iOS7中，上面这个方法已被新的API替代了。

```
- (CGRect)boundingRectWithSize:(CGSize)size options:(NSStringDrawingOptions)options attributes:(NSDictionary *)attributes context:(NSStringDrawingContext *)context
```

下面的代码是一个示例，大家可以做为参考：

```
CGSize expectedLabelSize = CGSizeZero;
    
    if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7) {
        NSMutableParagraphStyle *paragraphStyle = [[NSMutableParagraphStyle alloc] init];
        paragraphStyle.lineBreakMode = NSLineBreakByWordWrapping;
        NSDictionary *attributes = @{NSFontAttributeName:sideLabel.font, NSParagraphStyleAttributeName:paragraphStyle.copy};
        
        expectedLabelSize = [sideLabel.text boundingRectWithSize:CGSizeMake(260, 999) options:NSStringDrawingUsesLineFragmentOrigin attributes:attributes context:nil].size;
    }
    else {
        expectedLabelSize = [sideLabel.text sizeWithFont:sideLabel.font
                                       constrainedToSize:CGSizeMake(260, 999)
                                           lineBreakMode:sideLabel.lineBreakMode];
    }
```

这样就正确了。