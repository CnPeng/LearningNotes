* [原文链接：iOS Swift之Label详解学习](https://www.jianshu.com/p/c7f5dac44fb5)
* [相关链接：iOS Label详解学习_OC](https://www.jianshu.com/p/b294f385e07b)

// 1、创建

```swift
letrect:CGRect=CGRectMake(100,100,100,100)

letlabel:UILabel=UILabel(frame:rect)
```

// 2、设置和读取文本内容，默认为nil

```swift
label.text="文本信息"
```

// 3、设置文字颜色，默认为黑色

```swift
label.textColor=UIColor.redColor()
```

// 4、font设置文字大小，默认为17

```swift
label.font=UIFont.systemFontOfSize(20)//一般方法

label.font=UIFont.boldSystemFontOfSize(20)//加粗方法

label.font=UIFont.init(name:"Arial", size:20)//指定字体的方法

//label.font = [UIFont fontWithName:@"" size:16]; //指定字体的方法
```

// 5、textAlignment设置标签文本对齐方式

```swift
label.textAlignment=NSTextAlignment.Center

/**其他的对齐方式

NSTextAlignmentLeft= 0,// Visually left aligned

NSTextAlignmentCenter= 1,// Visually centered

NSTextAlignmentRight= 2,// Visually right aligned

NSTextAlignmentRight= 1,// Visually right aligned

NSTextAlignmentCenter= 2,// Visually centered

NSTextAlignmentJustified = 3,// Fully-justified. The last line in a paragraph is natural-aligned.

NSTextAlignmentNatural= 4,// Indicates the default alignment for script

*/
```

// 6、numberOfLines标签最多显示行数，如果为0则表示多行

```swift
label.numberOfLines=2
```

// 7、enabled只是决定了Label的绘制方式，将它设置为NO时文本变暗，表示没有激活，此时向它设置颜色值都是无效的。

```swift
label.enabled=false
```

// 8、highlighted是否高亮显示

```swift
label.highlighted=true;

label.highlightedTextColor=UIColor.orangeColor()//高亮显示时候的文本颜色
```

// 9、ShadowColor设置阴影颜色

```swift
label.shadowColor=UIColor.blackColor()
```

// 10、ShadowOffset设置阴影偏移量

```swift
label.shadowOffset=CGSizeMake(-1, -1)
```

// 11、baselineAdjustment 如果 ==YES，控制文本基线的行为

```swift
label.baselineAdjustment=UIBaselineAdjustment.None;

/*

UIBaselineAdjustmentAlignBaselines = 0, // default. used when shrinking text to position based on the original baseline默认，文本最上端与中线对齐。

UIBaselineAdjustmentAlignCenters, //文本中线与label中线对齐。

UIBaselineAdjustmentNone, //文本最低端与label中线对齐。

*/
```

// 12、Autoshrink是否自动收缩

```swift
/*

Fixed Font Size默认，如果label宽度小于文字长度时，文字大小不自动缩放

minimumScaleFactor设置最小收缩比例，如果Label宽度小于文字长度时，文字进行收缩，收缩超过比例后，停止收缩。

minimumFontSize设置最小收缩字号，如果label宽度小于文字长度时，文字字号减小，低于设定字号以后，不再减小。// 6.0以后不再使用了。

*/

label.minimumScaleFactor=0.5
```

// 13、`adjustsLetterSpacingToFitWidth` 改变字母之间的间距来适应 Label 大小

```swift
//label.adjustsLetterSpacingToFitWidth = YES;// NS_DEPRECATED_IOS(6_0,7_0) __TVOS_PROHIBITED

// Non-functional.Hand tune by using NSKernAttributeName to affect tracking, or consider using the allowsDefaultTighteningForTruncation property.
```

// 14、lineBreakMode设置文字过长时的显示格式

```swift
label.lineBreakMode=NSLineBreakMode.ByCharWrapping//以字符为显示单位显示，后面部分省略不显示

label.lineBreakMode=NSLineBreakMode.ByClipping//剪切与文本宽度相同的内容长度，后半部分被删除。

label.lineBreakMode=NSLineBreakMode.ByTruncatingHead//前面部分文字以……方式省略，显示尾部文字内容。

label.lineBreakMode=NSLineBreakMode.ByTruncatingMiddle//中间的内容以……方式省略，显示头尾的文字内容。

label.lineBreakMode=NSLineBreakMode.ByTruncatingTail//结尾部分的内容以……方式省略，显示头的文字内容。

label.lineBreakMode=NSLineBreakMode.ByWordWrapping//以单词为显示单位显示，后面部分省略不显示。
```

// 15、`adjustsFontSizeToFitWidth` 设置字体大小适应 label 宽度

```swift
label.adjustsFontSizeToFitWidth=true
```

// 16、attributedText设置标签属性文本

```swift
let text:NSString="doubiqiu"

let textLabelStr:NSMutableAttributedString=NSMutableAttributedString.init(string: textasString)

let bodyFont = [NSFontAttributeName:UIFont.preferredFontForTextStyle(UIFontTextStyleBody)]

textLabelStr.setAttributes(bodyFont, range:NSMakeRange(2,5))

label.attributedText= textLabelStr
```

// 17、竖排文字显示每个文字加一个换行符，这是最方便和简单的实现方式。

```swift
label.text="这\n个\n是\n竖\n排\n方\n向\n的\n显\n示"

label.numberOfLines=0
```

// 18、计算UILabel随字体多行后的高度

```swift
let bounds:CGRect=CGRectMake(0,0,200,300);

let heightLabel:CGRect= label.textRectForBounds(bounds, limitedToNumberOfLines:3)//计算20行之后的Label的Frame

print("%f",heightLabel.size.height)
```

// 19、UILabel根据字数多少自动实现适应高度

```swift
let msgLabel:UILabel=UILabel.init(frame:CGRectMake(15,170,0,0))

msgLabel.backgroundColor=UIColor.lightTextColor()

msgLabel.numberOfLines=0

msgLabel.lineBreakMode=NSLineBreakMode.ByWordWrapping

msgLabel.font=UIFont.init(name:"Arial", size:12)

let size:CGSize=CGSizeMake(290,1000)

msgLabel.text="获取到的deviceToken，我们可以通过webservice服务提交给.net应用程序，这里我简单处理，直接打印出来，拷贝到.net应用环境中使用。"

//CGSize msgSize = [msgLabel.text sizeWithFont:msgLabel.font constrainedToSize:size];

//msgLabel.frame = CGRectMake(15, 150, 290, msgSize.height)

//[msgLabel setFrame:CGRectMake(15, 150, 290, msgSize.height)];
```

// 20、渐变字体Label

```swift
//let img:UIImage = UIImage.init(named: "btn.png")!

//let titleColor:UIColor = UIColor.init(patternImage: img)

lettitleColor:UIColor=UIColor.init(patternImage:UIImage.init(named:"btn.png")!)

lettitle:NSString="Setting"

lettitleLabel:UILabel=UILabel.init(frame:CGRectMake(100,200,80,44))

titleLabel.textColor= titleColor

titleLabel.text= titleasString

titleLabel.font=UIFont.boldSystemFontOfSize(20)

titleLabel.backgroundColor=UIColor.clearColor()

self.view.addSubview(titleLabel)
```


// 21、Label添加边框

```swift
titleLabel.layer.borderColor=UIColor.grayColor().CGColor

titleLabel.layer.borderWidth=2
```

// 22、设置圆角

```swift
titleLabel.layer.cornerRadius=10

titleLabel.backgroundColor=UIColor.cyanColor()
```

// 23、设置背景色圆角

```swift
titleLabel.clipsToBounds=true

self.view.addSubview(label)

self.view.addSubview(msgLabel)
```