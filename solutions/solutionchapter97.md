### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-08-21 | Alfred Jiang | - |

### 方案名称
NSString - 删除 NSString 中特定字符

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
NSString \ 删除 \ 字符串

### 需求场景
1. 删除 NSString 中的数字、符号，或者修改其中的字符

### 参考链接
1. [iOS 删除NSString中特定字符](http://news.tuxi.com.cn/to/kf/satmam/pdhmhd.html)

### 详细内容

    +(NSString *) stringDeleteString:(NSString *)str
    {
        NSMutableString *str1 = [NSMutableString stringWithString:str];
        for (int i = 0; i < str1.length; i++) {
            unichar c = [str1 characterAtIndex:i];
            NSRange range = NSMakeRange(i, 1);
            if (c == '"' || c == '.' || c == ',' || c == '(' || c == ')') { //此处可以是任何字符
                [str1 deleteCharactersInRange:range];
                --i;
            }
        }
        NSString *newstr = [NSString stringWithString:str1];
        return newstr;
    }


### 效果图
（无）

### 备注
（无）
