### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-08-07 | Alfred Jiang | - |

### 方案名称
NSObject - 实现自定义对象 isEqual 方法

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
自定义对象 \ 相等 \ isEqual \ Hash

### 需求场景
1. 需要对自定义对象进行相等判断时
2. 需要对自定义对象数组进行是否包含判断时

### 参考链接
1. [CSDN - OC判断对象是否相等](http://blog.csdn.net/womendeaiwoming/article/details/46419323)
2. [CSDN - Objective-C 实现Equality and Hashing](http://blog.csdn.net/crayondeng/article/details/18818527)

### 详细内容

    @implementation Person

    - (BOOL)isEqual:(id)object {
      if (self == object) return YES;
      if (![object isKindOfClass:[Person class]]) return NO;

      return [self.name isEqualToString:[object name]];
    }

    - (NSUInteger)hash {
      return [self.name hash];
    }

    @end

### 效果图
（无）

### 备注
（无）
