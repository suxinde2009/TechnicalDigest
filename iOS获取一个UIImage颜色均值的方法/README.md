# iOS获取一个UIImage颜色均值的方法

在iOS判断颜色UIColor是浅色还是深色(DarkOrBright)中介绍了对颜色深浅的判断。那么，假如要判断背景图片颜色的深浅又该如何做呢？这里接啥一个获取UIImage颜色均值的方法，结合上一篇博文，便能很好的判断图片深浅：

	- (UIColor *)averageColorForImage:(UIImage*)image {
	    CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
	    unsigned char rgba[4];
	    CGContextRef context = CGBitmapContextCreate(rgba, 1, 1, 8, 4, colorSpace, kCGImageAlphaPremultipliedLast | kCGBitmapByteOrder32Big);
	 
	    CGContextDrawImage(context, CGRectMake(0, 0, 1, 1), image.CGImage);
	    CGColorSpaceRelease(colorSpace);
	    CGContextRelease(context);
	 
	    if(rgba[3] > 0) {
	        CGFloat alpha = ((CGFloat)rgba[3])/255.0;
	        CGFloat multiplier = alpha/255.0;
	        return [UIColor colorWithRed:((CGFloat)rgba[0])*multiplier
	                               green:((CGFloat)rgba[1])*multiplier
	                                blue:((CGFloat)rgba[2])*multiplier
	                               alpha:alpha];
	    }
	    else {
	        return [UIColor colorWithRed:((CGFloat)rgba[0])/255.0
	                               green:((CGFloat)rgba[1])/255.0
	                                blue:((CGFloat)rgba[2])/255.0
	                               alpha:((CGFloat)rgba[3])/255.0];
	    }
	}


http://www.travelchu.com/2014/08/19/ios%E8%8E%B7%E5%8F%96%E5%9B%BE%E7%89%87uiimage%E9%A2%9C%E8%89%B2%E5%9D%87%E5%80%BCaveragecolor/