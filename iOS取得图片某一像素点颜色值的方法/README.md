# iOS取得图片某一像素点颜色值的方法
本篇博文介绍一种取得图片某一像素点颜色值的方法，话不多说，直接上代码：

Get color of pixel

	- (UIColor*) getPixelColorAtLocation:(CGPoint)point inImage:(UIImage *)image {
	    UIColor* color = nil;
	    CGImageRef inImage = image.CGImage;
	    CGContextRef cgctx = [self createARGBBitmapContextFromImage:
	                          inImage];
	 
	    if (cgctx == NULL) {
	        return nil; /* error */
	    }
	    size_t w = CGImageGetWidth(inImage);
	    size_t h = CGImageGetHeight(inImage);
	    CGRect rect = {{0,0},{w,h}};
	 
	    CGContextDrawImage(cgctx, rect, inImage);
	    unsigned char* data = CGBitmapContextGetData (cgctx);
	    if (data != NULL) {
	        int offset = 4*((w*round(point.y))+round(point.x));
	        int alpha =  data[offset];
	        int red = data[offset+1];
	        int green = data[offset+2];
	        int blue = data[offset+3];
	        color = [UIColor colorWithRed:(red/255.0f) green:(green/255.0f) blue:
	                 (blue/255.0f) alpha:(alpha/255.0f)];
	    }
	    CGContextRelease(cgctx);
	    if (data) {
	        free(data);
	    }
	    return color;
	}
	 
	- (CGContextRef) createARGBBitmapContextFromImage:(CGImageRef) inImage {
	    CGContextRef context = NULL;
	    CGColorSpaceRef colorSpace;
	    void *bitmapData;
	    int bitmapByteCount;
	    int bitmapBytesPerRow;
	    size_t pixelsWide = CGImageGetWidth(inImage);
	    size_t pixelsHigh = CGImageGetHeight(inImage);
	    bitmapBytesPerRow = (pixelsWide * 4);
	    bitmapByteCount = (bitmapBytesPerRow * pixelsHigh);
	    colorSpace = CGColorSpaceCreateDeviceRGB();
	    if (colorSpace == NULL){
	        fprintf(stderr, "Error allocating color space\n");
	        return NULL;
	    }
	    bitmapData = malloc( bitmapByteCount );
	    if (bitmapData == NULL){
	        fprintf (stderr, "Memory not allocated!");
	        CGColorSpaceRelease( colorSpace );
	        return NULL;
	    }
	    context = CGBitmapContextCreate (bitmapData,pixelsWide,pixelsHigh,8,bitmapBytesPerRow,colorSpace,kCGImageAlphaPremultipliedFirst);
	    if (context == NULL){
	        free (bitmapData);
	        fprintf (stderr, "Context not created!");
	    }
	    CGColorSpaceRelease( colorSpace );
	    return context;
	}


http://www.travelchu.com/2014/08/18/ios%E8%8E%B7%E5%8F%96%E5%9B%BE%E7%89%87%E6%9F%90%E4%B8%80%E7%82%B9%E5%83%8F%E7%B4%A0%E9%A2%9C%E8%89%B2-get-color-of-pixel/