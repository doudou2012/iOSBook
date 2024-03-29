### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-26 | Alfred Jiang | - |

### 方案名称
NSString - 汉字转为拼音显示的实现

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
汉字 \ 拼音 \ 转换

### 需求场景
1. 需要将汉字转为英文拼音的场景

### 参考链接
1. [Code4App](http://code4app.com/ios/%E6%B1%89%E5%AD%97%E8%BD%AC%E6%8B%BC%E9%9F%B3/54055d31933bf0a3478b5413)
2. [GitHub : ChineseToPinYin](https://github.com/willonboy/ChineseToPinYin/tree/9cce145a85b56bb6893ef88fd64c911379f266a4)

### 详细内容

通过使用苹果类库CFStringTransform提供的方法实现汉字转拼音。通过kCFStringTransformMandarinLatin把汉字转换为中国拼音。通过kCFStringTransformStripDiacritics把中国拼音转换为英文字母。

    - (NSString *)hanziToPinyin:(NSString *)aHanZi
    {
        NSString *strResult = @"";

        if ([aHanZi length]) {
            NSMutableString *ms = [[NSMutableString alloc] initWithString:hanziText.text];
            if (CFStringTransform((__bridge CFMutableStringRef)ms, 0, kCFStringTransformMandarinLatin, NO)) {
                NSLog(@"pinyin: %@", ms);
            }
            if (CFStringTransform((__bridge CFMutableStringRef)ms, 0, kCFStringTransformStripDiacritics, NO)) {
                NSLog(@"pinyin: %@", ms);
            }
            strResult = [ms copy];
        }

        return strResult;
    }

### 效果图
（无）

### 备注
（无）
