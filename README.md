分享我在使用过程中的点滴心得:

1.SDWebImage框架自带实现处理好了图片离线缓存,以及会发生错位下载的情况,默认实现了数据的异步请求.
  使用过程中直接 sd_setImage:(NSString*)image placeholder:(NSString *)placeholder即可.
  
2.SDImageCache类有个单例sharedImageCache,用它能实现清除本地缓存和删除内存缓存等相关操作,还能利用getSize获得文件大小等等.
   NSUInteger size = [SDImageCache sharedImageCache].getSize;     // 图片缓存
    [[SDImageCache sharedImageCache] clearDisk];                   //清除磁盘缓存
    [[SDImageCache sharedImageCache] clearMemory];                 //清除内存缓存
    
    /**
 *  NSFileManager获取文件大小,跟
 */
- (void)getSize
{
    
    NSUInteger size = [SDImageCache sharedImageCache].getSize;     // 图片缓存
    [[SDImageCache sharedImageCache] clearDisk];                   //清除磁盘缓存
    [[SDImageCache sharedImageCache] clearMemory];                 //清除内存缓存
    
    
    NSFileManager *manager = [NSFileManager defaultManager];
    
    NSString *cache = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject];
    
    NSString *cachePath = [cache stringByAppendingPathComponent:@"default/com.hackemist.SDWebImageCache.default"];
    NSDirectoryEnumerator *fileEnumerator = [manager enumeratorAtPath:cachePath];
    
    
    NSInteger totlaSize = 0;
    for (NSString *fileName in fileEnumerator) {
        NSString *filePath = [cachePath stringByAppendingPathComponent:fileName];
        
        NSDictionary *attrs = [manager attributesOfItemAtPath:filePath error:nil];
        if ([attrs[NSFileType] isEqualToString:NSFileTypeDirectory]) {
            continue;
        }
        totlaSize += [attrs[NSFileSize] integerValue];
        
        }
   
    FXLog(@"%zd",totlaSize);

}
    
3.如果要使用图片下载缓存请包涵 : #import "UIImageView+WebCache.h"
  若果仅仅使用清除缓存请包涵   : #import "SDImageCache.h"
