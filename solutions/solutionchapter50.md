### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-26 | Alfred Jiang | - |

### 方案名称
语法 - 开发常用的宏定义

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
宏定义 \ 工具类

### 需求场景
1. 简化代码，提高统一，避免出错

### 参考链接
（无）

### 详细内容

#####1. Objective-C 版本

    #define NavigationBar_HEIGHT 44

    #define SCREEN_WIDTH ([UIScreen mainScreen].bounds.size.width)
    #define SCREEN_HEIGHT ([UIScreen mainScreen].bounds.size.height)
    #define SAFE_RELEASE(x) [x release];x=nil
    #define IOS_VERSION [[[UIDevice currentDevice] systemVersion] floatValue]
    #define CurrentSystemVersion ([[UIDevice currentDevice] systemVersion])
    #define CurrentLanguage ([[NSLocale preferredLanguages] objectAtIndex:0])

    #define BACKGROUND_COLOR [UIColor colorWithRed:242.0/255.0 green:236.0/255.0 blue:231.0/255.0 alpha:1.0]



    //use dlog to print while in debug model
    #ifdef DEBUG
    #   define DLog(fmt, ...) NSLog((@"%s [Line %d] " fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);
    #else
    #   define DLog(...)
    #endif


    #define isRetina ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(640, 960), [[UIScreen mainScreen] currentMode].size) : NO)
    #define iPhone5 ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(640, 1136), [[UIScreen mainScreen] currentMode].size) : NO)
    #define isPad (UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad)


    #if TARGET_OS_IPHONE
    //iPhone Device
    #endif

    #if TARGET_IPHONE_SIMULATOR
    //iPhone Simulator
    #endif


    //ARC
    #if __has_feature(objc_arc)
        //compiling with ARC
    #else
        // compiling without ARC
    #endif


    //G－C－D
    #define BACK(block) dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), block)
    #define MAIN(block) dispatch_async(dispatch_get_main_queue(),block)


    #define USER_DEFAULT [NSUserDefaults standardUserDefaults]
    #define ImageNamed(_pointer) [UIImage imageNamed:[UIUtil imageName:_pointer]]



    #pragma mark - common functions
    #define RELEASE_SAFELY(__POINTER) { [__POINTER release]; __POINTER = nil; }


    #pragma mark - degrees/radian functions
    #define degreesToRadian(x) (M_PI * (x) / 180.0)
    #define radianToDegrees(radian) (radian*180.0)/(M_PI)

    #pragma mark - color functions
    #define RGBCOLOR(r,g,b) [UIColor colorWithRed:(r)/255.0f green:(g)/255.0f blue:(b)/255.0f alpha:1]
    #define RGBACOLOR(r,g,b,a) [UIColor colorWithRed:(r)/255.0f green:(g)/255.0f blue:(b)/255.0f alpha:(a)]
    #define ITTDEBUG
    #define ITTLOGLEVEL_INFO     10
    #define ITTLOGLEVEL_WARNING  3
    #define ITTLOGLEVEL_ERROR    1

    #ifndef ITTMAXLOGLEVEL

    #ifdef DEBUG
        #define ITTMAXLOGLEVEL ITTLOGLEVEL_INFO
    #else
        #define ITTMAXLOGLEVEL ITTLOGLEVEL_ERROR
    #endif

    #endif

    // The general purpose logger. This ignores logging levels.
    #ifdef ITTDEBUG
      #define ITTDPRINT(xx, ...)  NSLog(@"%s(%d): " xx, __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__)
    #else
      #define ITTDPRINT(xx, ...)  ((void)0)
    #endif

    // Prints the current method's name.
    #define ITTDPRINTMETHODNAME() ITTDPRINT(@"%s", __PRETTY_FUNCTION__)

    // Log-level based logging macros.
    #if ITTLOGLEVEL_ERROR <= ITTMAXLOGLEVEL
      #define ITTDERROR(xx, ...)  ITTDPRINT(xx, ##__VA_ARGS__)
    #else
      #define ITTDERROR(xx, ...)  ((void)0)
    #endif

    #if ITTLOGLEVEL_WARNING <= ITTMAXLOGLEVEL
      #define ITTDWARNING(xx, ...)  ITTDPRINT(xx, ##__VA_ARGS__)
    #else
      #define ITTDWARNING(xx, ...)  ((void)0)
    #endif

    #if ITTLOGLEVEL_INFO <= ITTMAXLOGLEVEL
      #define ITTDINFO(xx, ...)  ITTDPRINT(xx, ##__VA_ARGS__)
    #else
      #define ITTDINFO(xx, ...)  ((void)0)
    #endif

    #ifdef ITTDEBUG
      #define ITTDCONDITIONLOG(condition, xx, ...) { if ((condition)) { \
                                                      ITTDPRINT(xx, ##__VA_ARGS__); \
                                                    } \
                                                  } ((void)0)
    #else
      #define ITTDCONDITIONLOG(condition, xx, ...) ((void)0)
    #endif

    #define ITTAssert(condition, ...)                                       \
    do {                                                                      \
        if (!(condition)) {                                                     \
            [[NSAssertionHandler currentHandler]                                  \
                handleFailureInFunction:[NSString stringWithUTF8String:__PRETTY_FUNCTION__] \
                                    file:[NSString stringWithUTF8String:__FILE__]  \
                                lineNumber:__LINE__                                  \
                                description:__VA_ARGS__];                             \
        }                                                                       \
    } while(0)



    #define _po(o) DLOG(@"%@", (o))
    #define _pn(o) DLOG(@"%d", (o))
    #define _pf(o) DLOG(@"%f", (o))
    #define _ps(o) DLOG(@"CGSize: {%.0f, %.0f}", (o).width, (o).height)
    #define _pr(o) DLOG(@"NSRect: {{%.0f, %.0f}, {%.0f, %.0f}}", (o).origin.x, (o).origin.x, (o).size.width, (o).size.height)

    #define DOBJ(obj)  DLOG(@"%s: %@", #obj, [(obj) description])

    #define MARK    NSLog(@"\nMARK: %s, %d", __PRETTY_FUNCTION__, __LINE__)

    #define LOADIMAGE(file,ext) [UIImage imageWithContentsOfFile:[[NSBundle mainBundle]pathForResource:file ofType:ext]]

    #define VIEWWITHTAG(_OBJECT, _TAG)    [_OBJECT viewWithTag : _TAG]

    // rgb颜色转换（16进制->10进制）
    #define UIColorFromRGB(rgbValue) [UIColor colorWithRed:((float)((rgbValue & 0xFF0000) >> 16))/255.0 green:((float)((rgbValue & 0xFF00) >> 8))/255.0 blue:((float)(rgbValue & 0xFF))/255.0 alpha:1.0]

    //RGB颜色
    #define RGB(RED,GREEN,BLUE) [UIColor colorWithRed:RED/255.00 green:GREEN/255.00 blue:BLUE/255.00 alpha:1.0]

### 效果图
（无）

### 备注
（无）
