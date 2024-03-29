### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-10-13 | Alfred Jiang | - |

### 方案名称
UIViewController - 旋转问题 willRotateToInterfaceOrientation 方法无法正常调用

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UIViewController \ NavigationController

### 需求场景
1. iPad 端某些 View 无法正常旋转

### 参考链接
1. [Stackoverflow - willRotateToInterfaceOrientation not being called from presented viewcontroller](http://stackoverflow.com/questions/14170404/willrotatetointerfaceorientation-not-being-called-from-presented-viewcontroller)

### 详细内容

#####可能原因：
1. iOS SDK 版本不对应，willRotateToInterfaceOrientation 方法在 iOS 8 以上已弃用。此时通过实现对应版本的推荐方法即可。
2. 没有正确的 addChildViewController。对数原因是这个，下面参考 [Stackoverflow](http://stackoverflow.com/questions/14170404/willrotatetointerfaceorientation-not-being-called-from-presented-viewcontroller) 梳理解决方法

#####解决方法
1. 当添加 NavigationController 类容器作为子容器时，或者通过 addSubview 方法加载某 UIViewController 的 view 作为某 view 的子 View 时，如果该容器或 ViewController 需要接受旋转通知，那么最好加上下面方法，以确定选装消息可以继续向下传递；
        [rootViewController addChildViewController:viewController];

2. 注册通知
        1. 确保 ViewDidLoad 方法实现了 [super ViewDidLoad]方法；
        2. 在 ViewDidLoad 中注册通知
                [[NSNotificationCenter defaultCenter] addObserver:self  selector:@selector(orientationChanged:)  name:UIDeviceOrientationDidChangeNotification  object:nil];}
        3. 实现消息接受
                - (void)orientationChanged:(NSNotification *)notification{
                    [self handleOrientation:[[UIApplication sharedApplication] statusBarOrientation]];
                }
        4. 实现对应处理
                 - (void) handleOrientation:(UIInterfaceOrientation) orientation {

                    if (orientation == UIInterfaceOrientationPortrait || orientation == UIInterfaceOrientationPortraitUpsideDown)
                    {
                        //handle the portrait view
                    }
                    else if (orientation == UIInterfaceOrientationLandscapeLeft || orientation == UIInterfaceOrientationLandscapeRight)
                    {
                        //handle the landscape view
                    }
                 }
        5. 别忘了在 dealloc 方法中移除监听
                [[NSNotificationCenter defaultCenter] removeObserver:self  name:UIDeviceOrientationDidChangeNotification  object:nil];}

### 效果图
（无）

### 备注
