### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-09-09 | Alfred Jiang | - |

### 方案名称
UIWebView - 使用 UIWebViewToFile 实现 UIWebView 内容转为 Image 或 PDF

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UIWebView \ Image \ PDF \ 长页面截图

### 需求场景
1. 需要将较长页面的 Web 内容转为 Image 或 PDF 文件时

### 参考链接
1. [GitHub - UIWebViewToFile](https://github.com/tracy-e/UIWebViewToFile)
2. [将UIWebView显示的内容转为图片和PDF](http://esoftmobile.com/2013/06/10/%E5%B0%86uiwebview%E6%98%BE%E7%A4%BA%E5%86%85%E5%AE%B9%E8%BD%AC%E4%B8%BA%E5%9B%BE%E7%89%87%E5%92%8Cpdf/)

### 详细内容

添加头文件
        #import "UIWebView+ToFile.h"

调用公共接口函数实现转换

```objective-c
@interface UIWebView (ToFile)

- (UIImage *)imageRepresentation;

- (NSData *)PDFData;

@end
```

### 效果图
（无）

### 备注
（无）
