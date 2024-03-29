### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-17 | Alfred Jiang | - |
| 2| 2015-05-07 | Alfred Jiang | - |

### 方案名称
UIView - 代码实现截图功能

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
代码截图 \ 截图 \ 页面图片截取

### 需求场景
1. 需要在应用内对某个 View 进行截图操作时

### 参考链接
1. [IOS开发之—程序截图](http://blog.csdn.net/pjk1129/article/details/7097618)
2. [Stackoverflow - Screenshot in swift iOS?](http://stackoverflow.com/questions/25444609/screenshot-in-swift-ios)

### 详细内容

#####1. 经典 Objective-C 解决方案

    //获得View图像
    - (UIImage *)imageFromView:(UIView *)theView
    {
        UIGraphicsBeginImageContext(theView.frame.size);
        CGContextRef context = UIGraphicsGetCurrentContext();
        [theView.layer renderInContext:context];
        UIImage *theImage = UIGraphicsGetImageFromCurrentImageContext();
        UIGraphicsEndImageContext();

        return theImage;
    }

    //获得View某个范围内的图像
    - (UIImage *)imageFromView:(UIView *)theView atFrame:(CGRect)r
    {
        UIGraphicsBeginImageContext(theView.frame.size);
        CGContextRef context = UIGraphicsGetCurrentContext();
        CGContextSaveGState(context);
        UIRectClip(r);
        [theView.layer renderInContext:context];
        UIImage *theImage = UIGraphicsGetImageFromCurrentImageContext();
        UIGraphicsEndImageContext();

        return  theImage;
    }

#####2. iOS7+ Swift 解决方案

    //
    //  UIView+Screenshot.swift
    //  VideoHouse
    //
    //  Created by gxw on 14/10/26.
    //  Copyright (c) 2014年 b-star. All rights reserved.
    //

    //实例一
    extension UIView {
        func screenshot() -> UIImage {
            var imageFrame = CGRectMake(0, 0, self.frame.size.width, self.frame.height)
            UIGraphicsBeginImageContextWithOptions(imageFrame.size, false, 0)
            self.drawViewHierarchyInRect(imageFrame, afterScreenUpdates: true)
            var screenshot = UIGraphicsGetImageFromCurrentImageContext()
            UIGraphicsEndImageContext()
            return screenshot
        }
    }

    //实例二
        func screenShotMethod() {
        //Create the UIImage
        UIGraphicsBeginImageContext(view.frame.size)
        view.layer.renderInContext(UIGraphicsGetCurrentContext())
        let image = UIGraphicsGetImageFromCurrentImageContext()
        UIGraphicsEndImageContext()

        //Save it to the camera roll
        UIImageWriteToSavedPhotosAlbum(image, nil, nil, nil)
    }

### 效果图
（无）

### 备注
（无）
