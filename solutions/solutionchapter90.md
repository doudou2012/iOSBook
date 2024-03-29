### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-05-07 | Alfred Jiang | - |
| 2 | 2015-08-18 | Alfred Jiang | 更新OC版本iOS6情况 |

### 方案名称
UILabel - 计算文本高度

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UILabel \ 高度 \ 文本高度 \ UILable 高度

### 需求场景
1. 需要根据文本内容动态修改控件高度时

### 参考链接
1. [iOS 7下计算文本高度注意事项](http://blog.csdn.net/pp204204/article/details/17711383)

### 详细内容

Objective-C 调用示例

    - (CGSize)string:(NSString *)string rectSize:(CGSize)upperSize font:(UIFont *)aFont
    {
        CGSize labelsize = CGSizeMake(0, 0);

        BOOL isIOS7 = ([getOsVersion() floatValue] >= 7.0);
        if (isIOS7) {
            NSDictionary *dic = [NSDictionary dictionaryWithObjectsAndKeys:aFont, NSFontAttributeName, nil];

            labelsize = [string boundingRectWithSize:upperSize
                                             options:\
                             NSStringDrawingTruncatesLastVisibleLine |
                             NSStringDrawingUsesLineFragmentOrigin |
                                NSStringDrawingUsesFontLeading
                                              attributes:dic
                                                 context:nil].size;
        }
        else
        {
            labelsize = [string sizeWithFont:aFont constrainedToSize:upperSize lineBreakMode:NSLineBreakByWordWrapping];
        }

        return labelsize;
    }

swift 调用示例（swift最低支持到iOS7，故不考虑iOS6情况）

    func sizeWithString(aString : NSString) -> CGFloat
    {
        let rect : CGSize = REXOCTools.string(aString, rectSize: CGSizeMake(self.labelRolesResponsibilities.frame.width, CGFloat.max), font: UIFont(name: "Helvetica-Light", size: 13.0))
        return ceil(rect.height)
    }



### 效果图
（无）

### 备注
（无）
