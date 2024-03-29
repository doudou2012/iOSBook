### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-04-24 | Alfred Jiang | - |

### 方案名称
其他 - 可视控件介绍与可定制替代方案推荐

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
系统控件 \ 控件 \ UIKit \ 自定义系统控件

### 需求场景
1. 需要对 SDK 所提供系统控件梳理了解时
2. 需要自定义系统控件时

### 参考链接

1. [Code4App](http://code4app.com/)
2. [iOS Developer Library](https://developer.apple.com/library/ios/navigation/)

### 详细内容

#### iPhone 与 iPad 共有可视控件

| 序号  | 控件 | Class | 替代增强方案 | 说明 |
|:-------------: |:---------------:|:-------------:|:-------------:|:---------------:|
| 1 | View  | [UIView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/) | | 视图控件 |
| 2 | Search Bar | [UISearchBar](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISearchBar_Class/)  |  | 搜索控件 |
| 3 | Tab Bar | [UITabBar](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITabBar_Class/) |  | 底部标签控件 |
| 4 | Tab Bar Item | [UITabBarItem](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITabBarItem_Class/) |  | 底部标签控件按钮项 |
| 5 | Toolbar | [UIToolbar](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIToolbar_Class/) |  | 底部工具栏控件 |
| 6 | Navigation Bar | [UINavigationBar](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationBar_Class/) |  | 导航控件 |
| 7 | Navigation Item | [UINavigationItem](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UINavigationItem_Class/) |  | 顶部导航控件按钮项 |
| 8 | Bar Button Item | [UIBarButtonItem](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIBarButtonItem_Class/) | [UIBarButtonItem-Badge](https://github.com/mikeMTOL/UIBarButtonItem-Badge) | 底部工具栏控件按钮项或顶部导航控件按钮项 |
| 9 | Web View | [UIWebView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIWebView_Class/) |  | 网页控件 |
| 10 | iAd BannerView | [ADBannerView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/ADBannerView_Class/) |  | iAd 广告控件 |
| 11 | MapKit View | [MKMapView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/MKMapView_Class/) |  | 地图控件 |
| 12 | Picker View | [UIPickerView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIPickerView_Class/) |  | 选择器控件 |
| 13 | Date Picker | [UIDatePicker](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIDatePicker_Class/) | [MGConferenceDatePicker](https://github.com/matteogobbi/MGConferenceDatePicker) | 日期选择器控件 |
| 14 | Scroll View | [UIScrollView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIScrollView_Class/) |  | 滑动视图控件 |
| 15 | Text View | [UITextView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextView_Class/) | [CYRTextView](https://github.com/illyabusigin/CYRTextView) | 段落文本编辑与显示控件 |
| 16 | Collection View | [UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_Class/) |  | 集合视图控件 |
| 17 | Image View | [UIImageView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImageView_Class/) | [SDWebImage](https://github.com/rs/SDWebImage) | 图片显示控件 |
| 18 | Table View | [UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/) |  | 列表现实控件 |
| 19 | Stepper | [UIStepper](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIStepper_Class/) |  | 增减计数控件 |
| 20 | Page Control | [UIPageControl](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIPageControl_Class/) | [SMPageControl](https://github.com/Spaceman-Labs/SMPageControl) | 页码展示控件 |
| 21 | Progress View | [UIProgressView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIProgressView_Class/) | [MBProgressHUD](https://github.com/jdg/MBProgressHUD) | 进度展示控件 |
| 22 | Activity Indicator View | [UIActivityIndicatorView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIActivityIndicatorView_Class/) | [MBProgressHUD](https://github.com/jdg/MBProgressHUD) | 旋转等待控件 |
| 23 | Switch | [UISwitch](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISwitch_Class/) | [KLSwitch](https://github.com/KieranLafferty/KLSwitch) | 开关控件 |
| 24 | Slider | [UISlider](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISlider_Class/) | [ASValueTrackingSlider](https://github.com/alskipp/ASValueTrackingSlider)| 滑动条控件 |
| 25 | Text Field | [UITextField](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextField_Class/) | [MaterialKit](https://github.com/nghialv/MaterialKit) | 文本输入控件 |
| 26 | Segmented Control | [UISegmentedControl](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISegmentedControl_Class/) |  | 分段控制控件 |
| 27 | Button | [UIButton](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIButton_Class/) | [MaterialKit](https://github.com/nghialv/MaterialKit) | 按钮控件 |
| 28 | Label | [UILabel](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/) | [MaterialKit](https://github.com/nghialv/MaterialKit) | 文本显示控件 |
| 29 | Alert View | [UIAlertView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAlertView_Class/) | [UIAlertView-Blocks](https://github.com/ryanmaxwell/UIAlertView-Blocks) | 弹出提示框控件  |
| 30 | Action Sheet | [UIActionSheet](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIActionSheet_Class/) | [AHKActionSheet](https://github.com/fastred/AHKActionSheet) | 底部弹出选择框控件 |

### 效果图
（无）

### 备注
