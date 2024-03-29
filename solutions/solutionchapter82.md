### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-04-25 | Alfred Jiang | - |

### 方案名称
内购 - iOS 内购的快速实现

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
内购 \ Purchase \ 应用内购买 \ In-App Purchases

### 需求场景
1. 需要实现应用内购买需求时

### 参考链接

1. [Cocos2d应用内购买及IAP](http://www.maiziedu.com/lesson/2202/)
2. [GitHub - MKStoreKit](https://github.com/MugunthKumar/MKStoreKit)
3. [In App Purchases 入门[译]](http://www.cnblogs.com/zilongshanren/archive/2012/01/15/2190193.html)([原文](http://www.raywenderlich.com/21081/introduction-to-in-app-purchases-in-ios-6-tutorial))
4. [iOS应用内置付费:In-App Purchases完全攻略(1)](http://mobile.51cto.com/hot-410061.htm)
5. [iOS应用内置付费:In-App Purchases完全攻略(2)](http://mobile.51cto.com/hot-410061_1.htm)
6. [In-App Purchase Programming Guide](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
7. [MKStoreKit小记](http://www.99css.com/1296/)

### 详细内容

#####1. 初始化

    [[MKStoreKit sharedKit] startProductRequest];

#####2. 获取 IAP 列表



    [[NSNotificationCenter defaultCenter] addObserverForName:kMKStoreKitProductsAvailableNotification
                                                      object:nil
                                                       queue:[[NSOperationQueue alloc] init]
                                                  usingBlock:^(NSNotification *note) {

                                                    NSLog(@"Products available: %@", [[MKStoreKit sharedKit] availableProducts]);
                                                  }];

#####3. 购买 IAP

    [[MKStoreKit sharedKit] initiatePaymentRequestForProductWithIdentifier:productIdentifier];

    [[NSNotificationCenter defaultCenter] addObserverForName:kMKStoreKitProductPurchasedNotification
                                                      object:nil
                                                       queue:[[NSOperationQueue alloc] init]
                                                  usingBlock:^(NSNotification *note) {

                                                      NSLog(@"Purchased/Subscribed to product with id: %@", [note object]);

                                                      NSLog(@"%@", [[MKStoreKit sharedKit] valueForKey:@"purchaseRecord"]);
                                                  }];

#####4. 恢复 IAP

    [[NSNotificationCenter defaultCenter] addObserverForName:kMKStoreKitRestoredPurchasesNotification
                                                      object:nil
                                                       queue:[[NSOperationQueue alloc] init]
                                                  usingBlock:^(NSNotification *note) {

                                                    NSLog(@"Restored Purchases");
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:kMKStoreKitRestoringPurchasesFailedNotification
                                                      object:nil
                                                       queue:[[NSOperationQueue alloc] init]
                                                  usingBlock:^(NSNotification *note) {

                                                     NSLog(@"Failed restoring purchases with error: %@", [note object]);
                                                  }];

### 效果图
（无）

### 备注
