### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-31 | Alfred Jiang | - |

### 方案名称
UILabel - 显示换行的方法

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UILabel \ 换行

### 需求场景
1. 需要对 UILable 中字符串进行换行显示时

### 参考链接
1. [CocoaChina](http://www.cocoachina.com/bbs/read.php?tid=3310)

### 详细内容

    UILabel*label;

    //设置换行
    label.lineBreakMode = UILineBreakModeWordWrap;
    label.numberOfLines = 0;

    换行符还是\n
    比如NSString * xstring=@"lineone\nlinetwo"

### 效果图
（无）

### 备注
