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
