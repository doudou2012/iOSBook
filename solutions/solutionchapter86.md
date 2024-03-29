### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-05-05 | Alfred Jiang | - |

### 方案名称
openURL - iOS App 跳转 App Store 下载和 App Store 评论

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
App Store \ 下载 \ 评论

### 需求场景
1. 需要进行 iOS 应用下载和应用评论场景

### 参考链接
1. [Presenting, Appirater](https://arashpayan.com/blog/2009/09/07/presenting-appirater/)

### 详细内容

| 版本 | 跳转App Store 下载 | 跳转App Store 评论 |
| -- | -- | -- |
| iOS 7 - |  http://itunes.apple.com/app/idAPP_ID |itms-apps://ax.itunes.apple.com/WebObjects/MZStore.woa/wa/viewContentsUserReviews?type=Purple+Software&id=APP_ID |
| iOS 7 + | http://itunes.apple.com/app/idAPP_ID | itms-apps://itunes.apple.com/app/idAPP_ID |
| iOS 8 + | http://itunes.apple.com/app/idAPP_ID | itms-apps://itunes.apple.com/WebObjects/MZStore.woa/wa/viewContentsUserReviews?id=APP_ID&onlyLatestVersion=true&pageNumber=0&sortOrdering=1&type=Purple+Software |

    NSString *templateReviewURLiOS7 = @"itms-apps://itunes.apple.com/app/idAPP_ID";
    NSString *templateReviewURLiOS8 = @"itms-apps://itunes.apple.com/WebObjects/MZStore.woa/wa/viewContentsUserReviews?id=APP_ID&onlyLatestVersion=true&pageNumber=0&sortOrdering=1&type=Purple+Software";

    NSString *reviewURL;
    if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7.0 && [[[UIDevice currentDevice] systemVersion] floatValue] < 7.1) {
        reviewURL = [templateReviewURLiOS7 stringByReplacingOccurrencesOfString:@"APP_ID" withString:[NSString stringWithFormat:@"%d", APPIRATER_APP_ID]];
    }
    // iOS 8 needs a different templateReviewURL also @see https://github.com/arashpayan/appirater/issues/182
    else if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 8.0)
    {
        reviewURL = [templateReviewURLiOS8 stringByReplacingOccurrencesOfString:@"APP_ID" withString:[NSString stringWithFormat:@"%d", APPIRATER_APP_ID]];
    }

    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:reviewURL]];

### 效果图
（无）

### 备注
（无）
