# Helpful
一些有用的内容

1、限制输入框输入位数（包含小数）
		<输入最多8位整数，2位小数>
		
	  - (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range 
	replacementString:(NSString *)string
	{
		//新输入的
		if (string.length == 0) {
			return YES;
		}

	   //第一个参数，被替换字符串的range
	   //第二个参数，即将键入或者粘贴的string
	   //返回的是改变过后的新str，即textfield的新的文本内容
		NSString *checkStr = [textField.text stringByReplacingCharactersInRange:range withString:string];

		//正则表达式（只支持两位小数）
		NSString *regex = @"^\\-?([1-9]\\d{0,7})(\\.\\d{0,2})?$";
	   //判断新的文本内容是否符合要求
		return [self isValid:checkStr withRegex:regex];

	}

	//检测改变过的文本是否匹配正则表达式，如果匹配表示可以键入，否则不能键入
	- (BOOL) isValid:(NSString*)checkStr withRegex:(NSString*)regex
	{
		NSPredicate *predicte = [NSPredicate predicateWithFormat:@"SELF MATCHES %@",regex];
		return [predicte evaluateWithObject:checkStr];
	}

2.学习git指令
	
	https://learngitbranching.js.org/?locale=zh_CN

3.iOS NSDate转成时间戳出现一年的误差
    
    第一种方法在转时间戳时使用的格式是：YYYY-MM-dd
    第二种方法的时间戳格式是：yyyy-MM-dd
    可以看到同一天打印的日期差距一年，这个bug太恶心，而且还是日期在年末的情况下才出现的。
    查了下YYYY-MM-dd和yyyy-MM-dd的区别，以下仅供参考：
    “YYYY format” 是 “ISO week numbering system”，“yyyy format” 是 “Gregorian Calendar”。
    以后处理时间戳可要注意了要使用小写的yyyy就行了
    
    
4.iOS 14下popToRootViewControllerAnimated:YES 导致TabBar隐藏的问题
解决方案：
- (void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated {
    if (self.viewControllers.count > 0) {
        // 当前导航栏, 只有第一个viewController push的时候设置隐藏
        if (self.viewControllers.count == 1) {
            viewController.hidesBottomBarWhenPushed = YES;
        }
    } else {
        viewController.hidesBottomBarWhenPushed = NO;
    }
    [super pushViewController:viewController animated:animated];
}

原理：
iOS14上，popToRoot或者popTo指定控制器，立马去拿viewControllers属性与iOS13有区别。iOS14会包含当前控制器，例如：A->B->C，这时候在C pop到A，viewControllers为[C,A]，在iOS13是[A]，而通过汇编查看了下系统的实现，系统会去拿数组中的控制器先翻转成[A,C]，然后遍历获取hidesBottomBarWhenPushed属性，因此A最外层会被C的hidesBottomBarWhenPushed覆盖，从而隐藏了tabbar，根据该思路，可以分类hook hidesBottomBarWhenPushed，判断当前控制器是否被pop了，从而返回最topViewController.hidesBottomBarWhenPushed

5.iOS Base64图片解码为空解决方案

问题描述

我们在将App中嵌入的H5页面中的图片通过App端原生分享到微信，H5端拼接生成的图片通过Base64编码传参给App端，编码后的Base64图片无法通过下面方案一进行解码，Base64字符串解码成NSData为nil，但在浏览器上通过Base64图片解码工具(站长工具-图片Base64编解码)可以正常显示图片，由此判断H5端base64编码没有问题，应该是App端解码的问题，后经查资料探索，通过下面的方案二可以解码成功。

解决思路

正常解码思路（方案一）：

/// base64字符串转图片
/// @param base64String 图片base64字符串
- (UIImage *)imageWithBase64String:(NSString *)base64String {    
 NSData *imageData = [[NSData alloc] initWithBase64EncodedString:base64String options:NSDataBase64DecodingIgnoreUnknownCharacters];
 UIImage *image = [UIImage imageWithData:imageData];
 return image;
}
DataURL解码（方案二）：

/// base64字符串转图片
/// @param base64String 图片base64字符串
- (UIImage *)imageWithBase64String:(NSString *)base64String {
 NSURL *URL = [NSURL URLWithString:base64String];
 NSData *imageData = [NSData dataWithContentsOfURL:URL];
 UIImage *image = [UIImage imageWithData:imageData];
 return image;
}
问题分析

H5端将图片转为Base64编码，是利用canvas.toDataURL()函数转换的：

dataURL = canvas.toDataURL('image/png'); //转换图片为dataURL,转换后为Base64格式
所以我们对应的解码也应该先把Base64字符串当做dataURL解码成URL，再转换为图片（解码方案二）。

总结

下次我们需要将Base64编码的图片还原成图片，首先需要弄清之前的图片是怎么转换成Base64的，我们再相应的去解码处理。如果不清楚原始图片的编码过程，我们可以先按照上面的方案一去尝试，如果转换失败，可以再尝试一下方案二，或者再探索探索其他的解决方案。

附：图片的Base64编码解码工具  http://tool.chinaz.com/tools/imgtobase/




