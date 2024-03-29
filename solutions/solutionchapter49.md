### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-25 | Alfred Jiang | - |

### 方案名称
NSArray - 对自定义对象的数组进行排序

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
自定义对象数组 \ 数组排序 \ 排序

### 需求场景
1. 需要对自定义对象的数组进行排序时

### 参考链接
1. [Cocoa Extensions in Swift](http://andelf.github.io/blog/2014/07/04/cocoa-in-swift/)
2. [NSString的常用用法](http://blog.csdn.net/hmt20130412/article/details/20449091)

### 详细内容

#####1. Swift 使用示例

1. 示例一

        self.allList.sortUsingComparator(
                    {
                        (s1:AnyObject!,s2:AnyObject!)->NSComparisonResult in
                        var obj1=s1 as CareerCheck
                        var obj2=s2 as CareerCheck
                        if obj1.cItemUpdateTime < obj2.cItemUpdateTime{
                            return NSComparisonResult.OrderedAscending
                        }else{
                            return NSComparisonResult.OrderedDescending
                        }
                })

1. 示例二

        self.auctionList.sortUsingComparator({

                (s1:AnyObject!,s2:AnyObject!)->NSComparisonResult in

                var obj1 = s1 as Auction
                var obj2 = s2 as Auction

                if aKey == "Name"
                {
                    return obj1.aName.compare(obj2.aName, options: NSStringCompareOptions.NumericSearch | NSStringCompareOptions.CaseInsensitiveSearch, range: nil, locale: nil)
                }
                else if aKey == "Time Left"
                {
                    return obj1.aEndTime.compare(obj2.aEndTime, options: NSStringCompareOptions.NumericSearch, range: nil, locale: nil)
                }
                else if aKey == "Lowest Bid"
                {
                    return obj1.aLowestBid.compare(obj2.aLowestBid, options: NSStringCompareOptions.NumericSearch, range: nil, locale: nil)
                }
                else if aKey == "My Bid"
                {
                    return obj1.aMyBid.compare(obj2.aMyBid, options: NSStringCompareOptions.NumericSearch, range: nil, locale: nil)
                }
                else
                {
                    return obj1.aMyRank.compare(obj2.aMyRank, options: NSStringCompareOptions.NumericSearch, range: nil, locale: nil)
                }
            })

#####2. Objective-C 使用示例
    arryA =  [arryB sortedArrayUsingComparator: ^(id obj1, id obj2) {
            if ([[obj1 index3] integerValue]> [[obj2 index3] integerValue]) {
                return (NSComparisonResult)NSOrderedDescending;
            }
            if ([[obj1 index3] integerValue]< [[obj2 index3] integerValue]) {
                return (NSComparisonResult)NSOrderedAscending;
            }
            return (NSComparisonResult)NSOrderedSame;
        }];

#####3. 函数使用说明

    func compare(aString: String, options mask: NSStringCompareOptions = default, range: Range<String.Index>? = default, locale: NSLocale? = default) -> NSComparisonResult

这是 Cocoa 在 Swift 中所添加的扩展之一，用于比较两个字符串

其中 NSStringCompareOptions 参数用于设置比较规则

| 可选项 | 说明 |
|:---|:---:|
| CaseInsensitiveSearch | 不区分大小写比较 |
| LiteralSearch | 区分大小写比较 |
| BackwardsSearch | 从字符串末尾开始搜索 |
| AnchoredSearch  | 搜索限制范围的字符串 |
| NumericSearch | 按照字符串里的数字为依据，算出顺序。例如 Foo2.txt < Foo7.txt < Foo25.txt |
| DiacriticInsensitiveSearch | 忽略 "-" 符号的比较 |
| WidthInsensitiveSearch | 忽略字符串的长度，比较出结果 |
| ForcedOrderingSearch | 忽略不区分大小写比较的选项，并强制返回 NSOrderedAscending 或者 NSOrderedDescending |
| RegularExpressionSearch | 只能应用于 rangeOfString:..., stringByReplacingOccurrencesOfString:...和 replaceOccurrencesOfString:... 方法。使用通用兼容的比较方法，如果设置此项，可以去掉 NSCaseInsensitiveSearch 和 NSAnchoredSearch |

Range 参数用于设置比较范围

[NSlocale](http://my.oschina.net/hmj/blog/126355) 参数用于提供于用户所处地域相关的定制化信息和首选项信息的设置


### 效果图
（无）

### 备注
（无）
