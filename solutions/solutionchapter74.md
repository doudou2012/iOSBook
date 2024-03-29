### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-04-22 | Alfred Jiang | - |

### 方案名称
推送 - 远程推送服务的测试与实现

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
推送 \ RemoteNotification \ 远程推送 \ 远程通知

### 需求场景
1. 需要对 APP 的推送功能进行开发与测试时

### 参考链接
1. [iOS推送小结--swift语言](http://www.cnblogs.com/maple023/p/4277505.html)
2. [(APNS) IOS远程推送测试工具SmartPush for Mac](http://www.cocoachina.com/bbs/read.php?tid-288780.html)
3. [使用pushmebaby测试app的远程推送功能](http://blog.diveinedu.net/pushmebaby_apns_notification/)
4. [Java实现IOS推送（Javapns2.2）](http://www.cnblogs.com/lihaozy/archive/2013/03/13/2957904.html)
5. [苹果推送通知服务教程 Apple Push Notification Services Tutorial](http://www.cnblogs.com/gpwzw/archive/2012/03/31/apple_push_notification_services_tutorial_part_1-2.html)
6. [iPhone推送通知功能分析总结](http://mtoou.info/iphone-tuisong/)

### 详细内容

##### 1. iOS 端推送的注册与实现

1. 注册相关推送证书([参考链接](http://www.cnblogs.com/maple023/p/4277505.html))

2. 在 AppDelegate 中实现以下步骤
        //1. 在 didFinishLaunchingWithOptions 函数中调用下面函数注册推送
        func registerPush()
        {
            if ((UIDevice.currentDevice().systemVersion as NSString).doubleValue >= 8.0) {
                UIApplication.sharedApplication().registerForRemoteNotifications()
                UIApplication.sharedApplication().registerUserNotificationSettings(UIUserNotificationSettings(forTypes:UIUserNotificationType.Badge|UIUserNotificationType.Sound|UIUserNotificationType.Alert, categories: nil))
            }
            else {
                UIApplication.sharedApplication().registerForRemoteNotificationTypes(UIRemoteNotificationType.Badge|UIRemoteNotificationType.Alert|UIRemoteNotificationType.Sound)
            }
        }

        //2. 实现下面三个 AppDelegate 函数
        func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData) {
            var token:String = deviceToken.description.stringByTrimmingCharactersInSet(NSCharacterSet(charactersInString: "<>"))
            println("token==\(token)")
            //将token发送到服务器
        }

        func application(application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: NSError) {
            var alert:UIAlertView = UIAlertView(title: "", message: error.localizedDescription, delegate: nil, cancelButtonTitle: "OK")
            alert.show()
            //注册失败提示
        }

        func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject]) {
            println("userInfo==\(userInfo)")
            //成功接收到推送，对应用内发送通知
            NSNotificationCenter.defaultCenter().postNotificationName(DID_RECEIVE_REMOTE_NOTIFICATION, object: nil, userInfo: userInfo)
        }

3. 在需要接收通知的类中实现接收监听
        //1. 在 viewDidLoad 函数中开启监听
        NSNotificationCenter.defaultCenter().addObserver(self, selector: Selector("receiveNotification:"), name: DID_RECEIVE_REMOTE_NOTIFICATION, object: nil)

        //2. 在 viewDidLoad 函数中开启监听
        func receiveNotification(aNotification : NSNotification)
        {
            //收到通知，对应处理操作
        }

4. 脚标清零
        func applicationDidBecomeActive(application: UIApplication) {
             application.applicationIconBadgeNumber = 0
        }

##### 2. 远程推送通知的测试与发送

1. 推荐使用 [SmartPush](https://github.com/shaojiankui/SmartPush) 具体使用步骤可参考[(APNS) IOS远程推送测试工具SmartPush for Mac ](http://www.cocoachina.com/bbs/read.php?tid-288780.html)

##### 3. 其他

######1. 推送格式举例：
    {"aps" : { "alert" : { "body" : "Bob wants toplay poker" }, "badge" : 5, "sound" :"bingbong.aiff"},    "acme1" : "bar", "acme2" : ["bang",  "whiz" ] }

######2. 注意

* device token 会在重装系统等特殊条件下发生改变，所以需要每次都通过 didRegisterForRemoteNotificationsWithDeviceToken 注册获取

* 推送通知的字节长度不能超过256个字节（256B）

* 推送通知不都是可靠的，苹果公司不保证传送每个通知以及通知到达的顺序。

* 当应用未启动时，仅接受并显示 "alert" 相关内容

* 当应用启动时，完整推送消息会被 didReceiveRemoteNotification 函数接受并通过 userInfo 参数获取

######3. 失败情况

* 苹果公司的反馈服务会报告通知的失败。

* 发布的接口是通过访问：feedback.push.apple.com, port2196。

* 开发的接口是通过访问：feedback.sandbox.push.apple.com, port2196。

* 注：Provider要反复监测苹果的反馈服务，来确保不向已经不存在的设备发送推送通知。

### 效果图
（无）

### 备注


