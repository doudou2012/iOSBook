### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-04-26 | Alfred Jiang | - |

### 方案名称
系统服务 - 调用系统应用和系统服务

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
系统服务 \ 相机 \ 短信 \ 录音 \ 位置服务

### 需求场景
1. 需要调用 iOS 系统应用和系统服务时

### 参考链接
1. [iOS开发长文--通讯录、蓝牙、内购、GameCenter、iCloud、Passbook系统服务开发汇总](http://www.cocoachina.com/ios/20150129/11068.html)
2. [iOS 8 新特性](http://www.cnblogs.com/fengquanwang/p/3998526.html)

### 详细内容

#### 调用系统应用

| 系统应用  | 调用方式  |
|:-------------: |:---------------|
| 电话 | tel:或者tel://、telprompt:或telprompt://(拨打电话前有提示) |
| 短信 | sms:或者sms:// |
| 邮件 | mailto:或者mailto:// |
| 浏览器 | http:或者http:// |

    //
    //  ViewController.m
    //  iOSSystemApplication
    //
    //  Created by Kenshin Cui on 14/04/05.
    //  Copyright (c) 2014年 cmjstudio. All rights reserved.
    //
    #import "ViewController.h"
    @interface ViewController ()
    @end
    @implementation ViewController
    - (void)viewDidLoad {
        [super viewDidLoad];
    }
    #pragma mark - UI事件
    //打电话
    - (IBAction)callClicK:(UIButton *)sender {
        NSString *phoneNumber=@"18500138888";
    //    NSString *url=[NSString stringWithFormat:@"tel://%@",phoneNumber];//这种方式会直接拨打电话
        NSString *url=[NSString stringWithFormat:@"telprompt://%@",phoneNumber];//这种方式会提示用户确认是否拨打电话
        [self openUrl:url];
    }
    //发送短信
    - (IBAction)sendMessageClick:(UIButton *)sender {
        NSString *phoneNumber=@"18500138888";
        NSString *url=[NSString stringWithFormat:@"sms://%@",phoneNumber];
        [self openUrl:url];
    }
    //发送邮件
    - (IBAction)sendEmailClick:(UIButton *)sender {
        NSString *mailAddress=@"kenshin@hotmail.com";
        NSString *url=[NSString stringWithFormat:@"mailto://%@",mailAddress];
        [self openUrl:url];
    }
    //浏览网页
    - (IBAction)browserClick:(UIButton *)sender {
        NSString *url=@"http://www.cnblogs.com/kenshincui";
        [self openUrl:url];
    }
    #pragma mark - 私有方法
    -(void)openUrl:(NSString *)urlStr{
        //注意url中包含协议名称，iOS根据协议确定调用哪个应用，例如发送邮件是“sms://”其中“//”可以省略写成“sms:”(其他协议也是如此)
        NSURL *url=[NSURL URLWithString:urlStr];
        UIApplication *application=[UIApplication sharedApplication];
        if(![application canOpenURL:url]){
            NSLog(@"无法打开\"%@\"，请确保此应用已经正确安装.",url);
            return;
        }
        [[UIApplication sharedApplication] openURL:url];
    }
    @end

#### 调用系统服务

| 系统服务  | Framework  |
|:--------------: |:---------------|
| 短信与邮件 | MessageUI.framework |
| 通讯录 | AddressBook.framework & AddressBookUI.framework |
| 蓝牙 | GameKit.framework & MultipeerConnectivity.framework & CoreBluetooth.framework |
| 社交 | Social.framework |
| Game Center | GameKit.framework |
| 应用内购买 | StoreKit.framework |
| iCloud | CloudKit.framework |
| Passbook | PassKit.framework |
| 动作感应 | CoreMotion.framework |
| 定位服务GPS | Core Location Framework |
| 播放视频 | AVKit Framework |
| 录制视频 | AV Foundation Framework |
| 音频相关 | CoreAudioKit Framework |

### 效果图
（无）

### 备注
