### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-01 | Alfred Jiang | - |

### 方案名称
UIButton - 避免多次重复点击

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UIButton \ 多次重复点击 \ 延时操作

### 需求场景
1. 避免短时间内多次重复点击按钮造成的响应错误

### 参考链接
（无）

### 详细内容
1. Swift 解决方案
        //
        //  UIButtonExtension.swift
        //  GrandJustice
        //
        //  Created by Alfred Jiang on 1/29/15.
        //  Copyright (c) 2015 FYH. All rights reserved.
        //

        import Foundation

        extension UIButton
        {
            func antiMultiplyTouch(delay : NSTimeInterval, closure:()->())
            {
                    self.userInteractionEnabled = false

                    dispatch_after(
                        dispatch_time(
                            DISPATCH_TIME_NOW,
                            Int64(delay * Double(NSEC_PER_SEC))
                        ),
                        dispatch_get_main_queue(), {
                            self.userInteractionEnabled = true
                            closure()
                    })
            }
        }

2. Objective-C 解决方案
        == .h文件
        //
        //  UIButton+AntiMultiplyTouch.h
        //  TestButton
        //
        //  Created by Alfred Jiang on 3/1/15.
        //  Copyright (c) 2015 Alfred Jiang. All rights reserved.
        //

        #import <UIKit/UIKit.h>

        @interface UIButton (antiMultiplyTouch)

        - (void)antiMultiplyTouch:(NSTimeInterval)delay block:(void(^)(void))operation;

        @end

        == .m文件
        #import "UIButton+AntiMultiplyTouch.h"

        @implementation UIButton (AntiMultiplyTouch)

        - (void)antiMultiplyTouch:(NSTimeInterval)delay block:(void(^)(void))operation
        {
            self.userInteractionEnabled = NO;

            dispatch_after(
                           dispatch_time(
                                         DISPATCH_TIME_NOW,
                                         delay * NSEC_PER_SEC
                                         ),
                           dispatch_get_main_queue(),
                           ^{
                                self.userInteractionEnabled = YES;
                                operation();
                            }
                           );
        }

        @end


### 效果图
（无）

### 备注
OC方案暂未测试
