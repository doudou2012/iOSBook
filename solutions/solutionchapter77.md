### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-04-23 | Alfred Jiang | - |

### 方案名称
推送 - 本地推送服务的测试与实现

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
推送 \ UILocalNotification \ 本地推送 \ 本地通知 \ 闹钟

### 需求场景
1. 需要实现类似闹铃功能的本地推送通知需求

### 参考链接
1. [GitHub - JRNLocalNotificationCenter](https://github.com/jarinosuke/JRNLocalNotificationCenter)
2. [CSDN - iOS本地推送与取消本地通知](http://blog.csdn.net/nsurl/article/details/12914141)
3. [CSDN - 闹钟app小结(ios)](http://blog.csdn.net/xie_kun/article/details/8886124)

### 详细内容

######1. JRNLocalNotificationCenter

1. JRNLocalNotificationCenter.h
        //
        //  JRNLocalNotificationCenter.h
        //  DemoApp
        //
        //  Created by jarinosuke on 7/27/13.
        //  Copyright (c) 2013 jarinosuke. All rights reserved.
        //

        #import <Foundation/Foundation.h>
        #import <UIKit/UIKit.h>

        extern NSString *const JRNLocalNotificationHandlingKeyName;
        extern NSString *const JRNApplicationDidReceiveLocalNotification;

        typedef void (^JRNLocalNotificationHandler)(NSString *key, NSDictionary *userInfo);

        @interface JRNLocalNotificationCenter : NSObject
        @property (nonatomic, copy) JRNLocalNotificationHandler localNotificationHandler;

        + (instancetype)defaultCenter;
        - (NSArray *)localNotifications;

        //Handling
        - (void)didReceiveLocalNotificationUserInfo:(NSDictionary *)userInfo;

        //Cancel
        - (void)cancelAllLocalNotifications;
        - (void)cancelLocalNotification:(UILocalNotification *)localNotification;
        - (void)cancelLocalNotificationForKey:(NSString *)key;

        //Post on now

        - (UILocalNotification *)postNotificationOnNowForKey:(NSString *)key
                                                   alertBody:(NSString *)alertBody;

        - (UILocalNotification *)postNotificationOnNowForKey:(NSString *)key
                                                   alertBody:(NSString *)alertBody
                                                    userInfo:(NSDictionary *)userInfo;

        - (UILocalNotification *)postNotificationOnNowForKey:(NSString *)key
                                                   alertBody:(NSString *)alertBody
                                                 alertAction:(NSString *)alertAction
                                                   soundName:(NSString *)soundName
                                                 launchImage:(NSString *)launchImage
                                                    userInfo:(NSDictionary *)userInfo
                                                  badgeCount:(NSUInteger)badgeCount
                                              repeatInterval:(NSCalendarUnit)repeatInterval;

        //Post on specified date

        - (UILocalNotification *)postNotificationOn:(NSDate *)fireDate
                                             forKey:(NSString *)key
                                          alertBody:(NSString *)alertBody;

        - (UILocalNotification *)postNotificationOn:(NSDate *)fireDate
                                             forKey:(NSString *)key
                                          alertBody:(NSString *)alertBody
                                           userInfo:(NSDictionary *)userInfo;

        - (UILocalNotification *)postNotificationOn:(NSDate *)fireDate
                                             forKey:(NSString *)key
                                          alertBody:(NSString *)alertBody
                                           userInfo:(NSDictionary *)userInfo
                                         badgeCount:(NSInteger)badgeCount;

        - (UILocalNotification *)postNotificationOn:(NSDate *)fireDate
                                             forKey:(NSString *)key
                                          alertBody:(NSString *)alertBody
                                        alertAction:(NSString *)alertAction
                                          soundName:(NSString *)soundName
                                        launchImage:(NSString *)launchImage
                                           userInfo:(NSDictionary *)userInfo
                                         badgeCount:(NSUInteger)badgeCount
                                     repeatInterval:(NSCalendarUnit)repeatInterval;

        @end

2. JRNLocalNotificationCenter.m
        //
        //  JRNLocalNotificationCenter.m
        //  DemoApp
        //
        //  Created by jarinosuke on 7/27/13.
        //  Copyright (c) 2013 jarinosuke. All rights reserved.
        //

        #import "JRNLocalNotificationCenter.h"

        NSString *const JRNLocalNotificationHandlingKeyName = @"JRN_KEY";
        NSString *const JRNApplicationDidReceiveLocalNotification = @"JRNApplicationDidReceiveLocalNotification";

        @interface JRNLocalNotificationCenter()
        @property (nonatomic) NSMutableDictionary *localPushDictionary;
        @property (nonatomic) BOOL checkRemoteNotificationAvailability;
        @end

        static JRNLocalNotificationCenter *defaultCenter;

        @implementation JRNLocalNotificationCenter

        + (instancetype)defaultCenter
        {
            static dispatch_once_t onceToken;
            dispatch_once(&onceToken, ^{
                defaultCenter = [JRNLocalNotificationCenter new];
                defaultCenter.localPushDictionary = [NSMutableDictionary new];
                [defaultCenter loadScheduledLocalPushNotificationsFromApplication];
                defaultCenter.checkRemoteNotificationAvailability = NO;
                defaultCenter.localNotificationHandler = nil;
            });
            return defaultCenter;
        }

        - (void)loadScheduledLocalPushNotificationsFromApplication
        {
            NSArray *scheduleLocalPushNotifications = [[UIApplication sharedApplication] scheduledLocalNotifications];
            for (UILocalNotification *localNotification in scheduleLocalPushNotifications) {
                if (localNotification.userInfo[JRNLocalNotificationHandlingKeyName]) {
                    [self.localPushDictionary setObject:localNotification forKey:localNotification.userInfo[JRNLocalNotificationHandlingKeyName]];
                }
            }
        }

        - (NSArray *)localNotifications
        {
            return [[NSArray alloc] initWithArray:[self.localPushDictionary allValues]];
        }

        - (void)didReceiveLocalNotificationUserInfo:(NSDictionary *)userInfo
        {
            NSString *key = userInfo[JRNLocalNotificationHandlingKeyName];
            if (!key) {
                return;
            }
            [self.localPushDictionary removeObjectForKey:key];

            [[NSNotificationCenter defaultCenter] postNotificationName:JRNApplicationDidReceiveLocalNotification
                                                                object:nil
                                                              userInfo:userInfo];

            if (self.localNotificationHandler) {
                self.localNotificationHandler(userInfo[JRNLocalNotificationHandlingKeyName], userInfo);
            }
        }

        - (void)cancelAllLocalNotifications
        {
            [[UIApplication sharedApplication] cancelAllLocalNotifications];
            [self.localPushDictionary removeAllObjects];
        }

        - (void)cancelLocalNotification:(UILocalNotification *)localNotification
        {
            if (!localNotification) {
                return;
            }

            [[UIApplication sharedApplication] cancelLocalNotification:localNotification];
            if (localNotification.userInfo[JRNLocalNotificationHandlingKeyName]) {
                [self.localPushDictionary removeObjectForKey:localNotification.userInfo[JRNLocalNotificationHandlingKeyName]];
            }
        }

        - (void)cancelLocalNotificationForKey:(NSString *)key
        {
            if (!self.localPushDictionary[key]) {
                return;
            }

            UILocalNotification *localNotification = self.localPushDictionary[key];
            [[UIApplication sharedApplication] cancelLocalNotification:localNotification];
            [self.localPushDictionary removeObjectForKey:key];
        }

        #pragma mark -
        #pragma mark - Post on now

        - (UILocalNotification *)postNotificationOnNowForKey:(NSString *)key
                                                   alertBody:(NSString *)alertBody
        {
            return [self postNotificationOnNow:YES
                                      fireDate:nil
                                        forKey:key
                                     alertBody:alertBody
                                   alertAction:nil
                                     soundName:nil
                                   launchImage:nil
                                      userInfo:nil
                                    badgeCount:0
                                repeatInterval:0];
        }

        - (UILocalNotification *)postNotificationOnNowForKey:(NSString *)key
                                  alertBody:(NSString *)alertBody
                                   userInfo:(NSDictionary *)userInfo
        {
            return [self postNotificationOnNow:YES
                                      fireDate:nil
                                        forKey:key
                                     alertBody:alertBody
                                   alertAction:nil
                                     soundName:nil
                                   launchImage:nil
                                      userInfo:userInfo
                                    badgeCount:0
                                repeatInterval:0];
        }

        - (UILocalNotification *)postNotificationOnNowForKey:(NSString *)key
                                  alertBody:(NSString *)alertBody
                                   userInfo:(NSDictionary *)userInfo
                                 badgeCount:(NSInteger)badgeCount
        {
            return [self postNotificationOnNow:YES
                                      fireDate:nil
                                        forKey:key
                                     alertBody:alertBody
                                   alertAction:nil
                                     soundName:nil
                                   launchImage:nil
                                      userInfo:userInfo
                                    badgeCount:badgeCount
                                repeatInterval:0];
        }

        - (UILocalNotification *)postNotificationOnNowForKey:(NSString *)key
                                  alertBody:(NSString *)alertBody
                                alertAction:(NSString *)alertAction
                                  soundName:(NSString *)soundName
                                launchImage:(NSString *)launchImage
                                   userInfo:(NSDictionary *)userInfo
                                 badgeCount:(NSUInteger)badgeCount
                             repeatInterval:(NSCalendarUnit)repeatInterval
        {
            return [self postNotificationOnNow:YES
                                      fireDate:nil
                                        forKey:key
                                     alertBody:alertBody
                                   alertAction:alertAction
                                     soundName:soundName
                                   launchImage:launchImage
                                      userInfo:userInfo
                                    badgeCount:badgeCount
                                repeatInterval:repeatInterval];
        }

        #pragma mark -
        #pragma mark - Post on specified date

        - (UILocalNotification *)postNotificationOn:(NSDate *)fireDate
                            forKey:(NSString *)key
                         alertBody:(NSString *)alertBody
        {
            return [self postNotificationOnNow:NO
                                      fireDate:fireDate
                                        forKey:key
                                     alertBody:alertBody
                                   alertAction:nil
                                     soundName:nil
                                   launchImage:nil
                                      userInfo:nil
                                    badgeCount:0
                                repeatInterval:0];
        }

        - (UILocalNotification *)postNotificationOn:(NSDate *)fireDate
                            forKey:(NSString *)key
                         alertBody:(NSString *)alertBody
                          userInfo:(NSDictionary *)userInfo
        {
            return [self postNotificationOnNow:NO
                                      fireDate:fireDate
                                        forKey:key
                                     alertBody:alertBody
                                   alertAction:nil
                                     soundName:nil
                                   launchImage:nil
                                      userInfo:userInfo
                                    badgeCount:0
                                repeatInterval:0];
        }

        - (UILocalNotification *)postNotificationOn:(NSDate *)fireDate
                            forKey:(NSString *)key
                         alertBody:(NSString *)alertBody
                          userInfo:(NSDictionary *)userInfo
                        badgeCount:(NSInteger)badgeCount
        {
            return [self postNotificationOnNow:NO
                                      fireDate:fireDate
                                        forKey:key
                                     alertBody:alertBody
                                   alertAction:nil
                                     soundName:nil
                                   launchImage:nil
                                      userInfo:userInfo
                                    badgeCount:badgeCount
                                repeatInterval:0];
        }

        - (UILocalNotification *)postNotificationOn:(NSDate *)fireDate
                            forKey:(NSString *)key
                         alertBody:(NSString *)alertBody
                       alertAction:(NSString *)alertAction
                         soundName:(NSString *)soundName
                       launchImage:(NSString *)launchImage
                          userInfo:(NSDictionary *)userInfo
                        badgeCount:(NSUInteger)badgeCount
                    repeatInterval:(NSCalendarUnit)repeatInterval
        {
            return [self postNotificationOnNow:NO
                                      fireDate:fireDate
                                        forKey:key
                                     alertBody:alertBody
                                   alertAction:alertAction
                                     soundName:soundName
                                   launchImage:launchImage
                                      userInfo:userInfo
                                    badgeCount:badgeCount
                                repeatInterval:repeatInterval];
        }

        - (UILocalNotification *)postNotificationOnNow:(BOOL)presentNow
                             fireDate:(NSDate *)fireDate
                               forKey:(NSString *)key
                            alertBody:(NSString *)alertBody
                          alertAction:(NSString *)alertAction
                            soundName:(NSString *)soundName
                          launchImage:(NSString *)launchImage
                             userInfo:(NSDictionary *)userInfo
                           badgeCount:(NSUInteger)badgeCount
                       repeatInterval:(NSCalendarUnit)repeatInterval;
        {
            if (self.localPushDictionary[key]) {
                //same key already exists
                return self.localPushDictionary[key];
            }

            UILocalNotification *localNotification = [[UILocalNotification alloc] init];
            if (!localNotification) {
                return nil;
            }

            UIRemoteNotificationType notificationType = [[UIApplication sharedApplication] enabledRemoteNotificationTypes];
            if (self.checkRemoteNotificationAvailability && notificationType == UIRemoteNotificationTypeNone) {
                return nil;
            }

            BOOL needsNotify = NO;

            //Alert
            if (self.checkRemoteNotificationAvailability && (notificationType & UIRemoteNotificationTypeAlert) != UIRemoteNotificationTypeAlert) {
                needsNotify = NO;
            } else {
                needsNotify = YES;
            }
            //add key name for handling it.
            NSMutableDictionary *userInfoAddingHandlingKey = [NSMutableDictionary dictionaryWithDictionary:userInfo];
            [userInfoAddingHandlingKey setObject:key forKey:JRNLocalNotificationHandlingKeyName];
            localNotification.userInfo         = userInfoAddingHandlingKey;
            localNotification.alertBody        = alertBody;
            localNotification.alertAction      = alertAction;
            localNotification.alertLaunchImage = launchImage;
            localNotification.repeatInterval   = repeatInterval;

            //Sound
            if (self.checkRemoteNotificationAvailability && (notificationType & UIRemoteNotificationTypeSound) != UIRemoteNotificationTypeSound) {
                needsNotify = NO;
            } else {
                needsNotify = YES;
            }
            if (soundName) {
                localNotification.soundName = soundName;
            } else {
                localNotification.soundName = UILocalNotificationDefaultSoundName;
            }

            //Badge
            if (self.checkRemoteNotificationAvailability && (notificationType & UIRemoteNotificationTypeBadge) != UIRemoteNotificationTypeBadge) {
            } else {
                localNotification.applicationIconBadgeNumber = badgeCount;
            }

            if (needsNotify) {
                if (presentNow && !fireDate) {
                    [[UIApplication sharedApplication] presentLocalNotificationNow:localNotification];
                } else {
                    localNotification.fireDate = fireDate;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];
                }
                [self.localPushDictionary setObject:localNotification forKey:key];
                return localNotification;
            } else {
                return nil;
            }
        }
        @end

######2.  使用举例：REXLocalNotificationManager

    //
    //  REXLocalNotificationManager.swift
    //  REX
    //
    //  Created by Alfred Jiang on 4/23/15.
    //  Copyright (c) 2015 REX. All rights reserved.
    //

    import UIKit

    class REXLocalNotificationManager: NSObject {

        var keysForAuctions : NSMutableArray = []

        class var sharedInstance : REXLocalNotificationManager {
            struct Static {
                static var onceToken : dispatch_once_t = 0
                static var instance : REXLocalNotificationManager? = nil
            }
            dispatch_once(&Static.onceToken) {
                Static.instance = REXLocalNotificationManager()
            }
            return Static.instance!
        }

        func cancelAllAuctionsLocalNotification()
        {
            for key in keysForAuctions
            {
                JRNLocalNotificationCenter.defaultCenter().cancelLocalNotificationForKey(key as String)
            }
        }

        func addNotificationsForList(auctions : NSArray)
        {
            self.cancelAllAuctionsLocalNotification()

    //        let keyEnded : String = "Test_Auction_End_Time"
    //        self.addPost(keyEnded, aTime: NSDate(timeIntervalSinceNow:30.0),aBody: "Test Auction ended")

            for obj in auctions
            {
                var aAuction = obj as Auction

                if aAuction.aAuctionStatus == "0"   //进行中
                {
                    let keyEnded : String = "Auction_End_Time_\(aAuction.aId)"
                    self.addPost(keyEnded, aTime: aAuction.endDateValue(0),aBody: "Auction ended")

                    let keyEnded_1_Hour : String = "Auction_End_1_Hour_Time_\(aAuction.aId)"
                    self.addPost(keyEnded_1_Hour, aTime: aAuction.endDateValue(60 * 60),aBody: "\(aAuction.aName) will end in 1 hour")

                    let keyEnded_3_Hour : String = "Auction_End_3_Hour_Time_\(aAuction.aId)"
                    self.addPost(keyEnded_3_Hour, aTime: aAuction.endDateValue(3 * 60 * 60),aBody: "\(aAuction.aName) will end in 3 hours")

                    let keyEnded_24_Hour : String = "Auction_End_24_Hour_Time_\(aAuction.aId)"
                    self.addPost(keyEnded_24_Hour, aTime: aAuction.endDateValue(24 * 60 * 60),aBody: "\(aAuction.aName) will end in 24 hours")

                }
                else if aAuction.aAuctionStatus == "1"  //未开始
                {
                    let keyEnded : String = "Auction_Start_Time_\(aAuction.aId)"
                    self.addPost(keyEnded, aTime: aAuction.startDateValue(0),aBody: "Auction start")

                    let keyEnded_1_Hour : String = "Auction_Start_1_Hour_Time_\(aAuction.aId)"
                    self.addPost(keyEnded_1_Hour, aTime: aAuction.startDateValue(60 * 60),aBody: "\(aAuction.aName) will start 1 hour later")

                    let keyEnded_3_Hour : String = "Auction_Start_3_Hour_Time_\(aAuction.aId)"
                    self.addPost(keyEnded_3_Hour, aTime: aAuction.startDateValue(3 * 60 * 60),aBody: "\(aAuction.aName) will start 3 hours later")

                    let keyEnded_24_Hour : String = "Auction_Start_24_Hour_Time_\(aAuction.aId)"
                    self.addPost(keyEnded_24_Hour, aTime: aAuction.startDateValue(24 * 60 * 60),aBody: "\(aAuction.aName) will start 24 hours later")
                }
            }
        }

        func addPost(aKey : NSString, aTime : NSDate, aBody : NSString)
        {
            let dateNow : NSDate = NSDate()
            let overTime : Bool = aTime.timeIntervalSince1970 - dateNow.timeIntervalSince1970 > 0

            if !overTime
            {
                return
            }

            JRNLocalNotificationCenter.defaultCenter().postNotificationOn(aTime, forKey: aKey, alertBody: aBody)
            self.keysForAuctions.addObject(aKey)
        }

    }

######3. 接受通知

    func application(application: UIApplication, didReceiveLocalNotification notification: UILocalNotification) {
        println("Local Notification userInfo==\(notification.userInfo)")
        let body : String = notification.alertBody! as String
        var alertView : UIAlertView = UIAlertView(title: "", message: body, delegate: nil, cancelButtonTitle: "OK")
        alertView.show()
    }

######4. 闹钟实现注意事项
使用LocalNotification 实现闹钟时需要注意以下事项

1. 本地的apns最多只能设置 64 个，如果超过了的话，以最新设置的 64 个apns为准，最早设置的会被忽略，这个当初没有仔细看文档，导致了不该出现的bug，后文会讲到

2. 当在后台收到apns时，发出的apns声音不能超过 30s,如果指定的apns的sound文件的实际播放声音超过 30s，则只播放 30s

3. 删除app重新下载后，原先设置的本地apns还在，这个当时也没注意到，吃了大亏，后来一查才知，本地的apns是系统进程，不会随着你的app删除而消失，不过如果你的app被删除，原先存在的apns是不会被触发的!

4. 闹钟的功能是以叫醒人为主，但是很遗撼，每个UILocalization的声音最多只能响一次，app推出后，不少用户也抱怨此功能不人性化，现在我们的做法是将６４个本地的apns全部分配给最近的那个闹钟，比如我设置在13:00响铃，声音的实际播放时间为３０s，这样每隔３０s推送一次本地的apns，这样６４次就足以将用户叫醒（当然如果在此期间用户没将手机带在身边，那就悲剧了，下次就不会再响了，不过此概率极小），如果用户点击apns进入app，那就cancel全部的UILocalization,然后再重新设置最新的闹钟，将６４个apns全部赋予此闹钟

5. 计算最近的闹钟有一个问题，如何判定这个最近？比如有的闹钟是今天，有的闹钟是昨天（但此闹钟设置为每天响铃），我的做法是，将所有重复闹钟响铃时间的年月日全部换成今天的年月日（时分秒不变），然后再与today[NSDate date]进行比较，如果比today早，则将响铃时间设置为之后最近的会响铃的那一天，这样设置之后，就可以获取最近的闹钟时间了

6. 何时重新设置这个闹钟时间，通常的做法是在收到UILocalization(即application:didReceiveLocalNotification:)时重新设置，但是这有一个问题，此方法只有当用户点了apns的提示时（或在锁屏状态下滑动进入app）时才能触发，也就是说，如果在后台收到了apns，但用户是通过点击app的icon进入app的话，此方法是不会触发的！这也是苹果的一个bug,所以我的做法是在application:didReceiveLocalNotification:此方法里触发，也在applicationdidbecomeactive方法里触发！这样就可以解决此问题了


### 效果图
（无）

### 备注
（无）
