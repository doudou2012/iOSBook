### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-10-19 | Alfred Jiang | - |

### 方案名称
版本兼容 - 7/8 - 无法正确获取 iPad 横竖屏宽高解决方案

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
iPad \ 横竖屏 \ 宽高

### 需求场景
1. 需要获取横竖屏宽高时

### 参考链接
1. [CocoaChina - 主题 : iPad下ios7的app.window一直是竖屏，怎么设置横屏](http://www.cocoachina.com/bbs/read.php?tid-281912.html)

### 详细内容

// 检测版本
inline static int CHECK_IOS() {
int v = [[getOsVersion() substringToIndex:3] floatValue] * 10;
return v;
}

#pragma mark - Use to get corrent screen bounds

- (BOOL)isLandscape {
UIInterfaceOrientation orientation = [[UIApplication sharedApplication] statusBarOrientation];
return orientation == UIInterfaceOrientationLandscapeLeft
|| orientation == UIInterfaceOrientationLandscapeRight;
}

- (CGRect)screenBounds {
BOOL isLandscape = [self isLandscape];
CGRect screenBounds = [UIScreen mainScreen].bounds;
float screenWidth = isLandscape ? screenBounds.size.height : screenBounds.size.width;
float screenHeight = isLandscape ? screenBounds.size.width : screenBounds.size.height;
if (CHECK_IOS() >= 80) {
screenWidth = screenBounds.size.width;
screenHeight = screenBounds.size.height;
}
return CGRectMake(0, 0, screenWidth, screenHeight);
}

### 效果图
（无）

### 备注
（无）
