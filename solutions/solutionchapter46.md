### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-23 | Alfred Jiang | - |
| 2 | 2015-03-31 | Alfred Jiang | 更新注意问题1 |

### 方案名称
UINavigationBar - 自定义按钮和标题

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UINavigationBar \ navigationItem \ titleView

### 需求场景
1. 需要自定义 UINavigationBar 的按钮和标题等

### 参考链接
1. [自定义UINavigationBar的属性]()

### 详细内容

#### Swift 版本

#####1. 通过 xib 文件自定义 navigationItem 的 titleView

1. 实例化自定义对象
        var menuTitle : REXMenuBtnView = REXMenuBtnView.loadFromNibNamed("REXMenuBtnView", bundle: nil) as REXMenuBtnView

2. 在 viewDidLoad 中初始化必要的属性
        menuTitle.btnTitle.addTarget(self, action: Selector("clickTitleBtn:"), forControlEvents: UIControlEvents.TouchUpInside)    //示例添加按钮响应
        menuTitle.setSelected(false)    //示例调用方法

3. 在 viewDidLayoutSubviews 中赋值 navigationItem
        self.navigationItem.titleView = menuTitle

4. Swift 下载入自定义 xib View 方法
        extension UIView {
            class func loadFromNibNamed(nibNamed: String, bundle : NSBundle? = nil) -> UIView? {
                return UINib(nibName: nibNamed,bundle: bundle).instantiateWithOwner(nil, options: nil)[0] as? UIView
            }
        }

#####2. 通过 UILabel 修改 navigationItem 的 TitleView

        var labelTitle : UILabel = UILabel(frame: CGRectMake(0, 0, 100, 44))
        labelTitle.font = UIFont(name: "Georgia-Italic", size: 18.0)
        labelTitle.textColor = UIColor(red: 73.0 / 255.0, green: 73.0 / 255.0, blue: 73.0 / 255.0, alpha: 1.0)
        labelTitle.backgroundColor = UIColor.clearColor()
        labelTitle.textAlignment = NSTextAlignment.Center
        labelTitle.text = "Message"
        self.navigationItem.titleView = labelTitle

#####3. 自定义 NavigationBar 背景图

        self.navigationController?.navigationBar.setBackgroundImage(UIImage(named: "login_Bg@2x"), forBarMetrics: UIBarMetrics.Default)

#####4. 自定义 NavigationBar 按钮

        var backBtn : MKButton = REXTools.navRoundBtn("",frame : CGRectMake(0, 0, 12, 20), color: COLOR_GRAY,imageName: "arrowLeft", target: self, action: Selector("clickBackBtn:"))
        var tempItem : UIBarButtonItem = UIBarButtonItem(customView: backBtn)
        self.navigationItem.leftBarButtonItems = [tempItem]

#### Objective-C 版本

    //NavigationBar设置背景图
    [__navigationBar__ setBackgroundImage:[UIImage imageNamed:@"__导航条背景图__"] forBarMetrics:UIBarMetricsDefault];

    //LeftButton设置属性
    UIButton *leftButton = [UIButton buttonWithType:UIButtonTypeCustom];
    [leftButton setFrame:CGRectMake(0, 0, __leftButtonWidth__, __leftButtonHeight__)];
    [leftButton setBackgroundImage:[UIImage imageNamed:@"__左按钮背景图__"] forState:UIControlStateNormal];
    [leftButton addTarget:self action:@selector(__leftButtonSelector__) forControlEvents:UIControlEventTouchUpInside];
    [__leftBarButton__ setCustomView:leftButton];

    //RightButton设置属性
    UIButton *rightButton = [UIButton buttonWithType:UIButtonTypeCustom];
    [rightButton setFrame:CGRectMake(0, 0, __rightButtonWidth__, __rightButtonHeight__)];
    [rightButton setBackgroundImage:[UIImage imageNamed:@"__右按钮背景图__"] forState:UIControlStateNormal];
    [rightButton addTarget:self action:@selector(__rightBarButton__) forControlEvents:UIControlEventTouchUpInside];
    [__rightBarButton__ setCustomView:rightButton];

    //NavigationItem设置属性
    UILabel *titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, 320, 44)];
    titleLabel.font = [UIFont boldSystemFontOfSize:__fontOfSize__];
    titleLabel.textColor = __textColor__;
    titleLabel.backgroundColor = [UIColor clearColor];
    titleLabel.textAlignment = UITextAlignmentCenter;
    titleLabel.text = @"__text__";
    __navigationItem__.titleView = titleLabel;

#### 注意问题

#####1. 设置 translucent 属性避免 UIView 向上偏移

    UINavigationBar.appearance().translucent = false

以上写法在 iOS 7 环境下易造成 Crash，需要加如下条件

    if (UIDevice.currentDevice().systemVersion as NSString).floatValue >= 8.0 {

            UINavigationBar.appearance().translucent = false
        }

或者使用下面的写法设置避免 UIView 向上偏移

     self.navigationController?.navigationBar.translucent = false

### 效果图
（无）

### 备注
（无）
