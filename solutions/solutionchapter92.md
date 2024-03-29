### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-06-09 | Alfred Jiang | - |

### 方案名称
NSMutableSet - 在NSMutableSet中添加自定义对象时怎么保证不重复

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
NSMutableSet \ hash \ isEqual \ 重复

### 需求场景
1. 需要将自定义对象添加入 NSMutableSet，需要保证不重复
2. 避免存储自定义重复对象

### 参考链接
1. [NSHipster - Equality](http://nshipster.com/equality/)

### 详细内容

通过自定义 hasn 与 isEqual 方法定义相等条件

示例代码如下：

ALFImport.h

    //
    //  ALFImport.h
    //  ALFUMLTool
    //
    //  Created by Alfred Jiang on 6/8/15.
    //  Copyright (c) 2015 Alfred Jiang. All rights reserved.
    //

    #import <Foundation/Foundation.h>

    typedef NS_ENUM(NSInteger, ImportType) {
        IMPORT_TYPE_SYSTEM = 0,
        IMPORT_TYPE_USER,
        IMPORT_TYPE_WEAK
    };

    @interface ALFImport : NSObject
    {
        ImportType type;
        NSString *name;
    //    NSArray *classes;
    }

    @property(nonatomic,assign) ImportType type;
    @property(nonatomic,strong) NSString *name;
    //@property(nonatomic,strong) NSArray *classes;

    - (NSUInteger)hash;
    - (BOOL)isEqual:(id)object;
    - (NSString *)description;

    @end

ALFImport.m

    //
    //  ALFImport.m
    //  ALFUMLTool
    //
    //  Created by Alfred Jiang on 6/8/15.
    //  Copyright (c) 2015 Alfred Jiang. All rights reserved.
    //

    #import "ALFImport.h"

    @implementation ALFImport
    @synthesize type;
    @synthesize name;
    //@synthesize classes;

    - (BOOL)isEqualToImport:(ALFImport *)import {
        if (!import) {
            return NO;
        }

        BOOL haveEqualType = (!self.type && !import.type) || (type == import.type);
        BOOL haveEqualName = (!self.name && !import.name) || [self.name isEqualToString:import.name];

        return haveEqualType && haveEqualName;
    }

    - (NSUInteger)hash
    {
         return self.type ^ [self.name hash];
    }

    - (BOOL)isEqual:(id)object
    {
        if (self == object) {
            return YES;
        }

        if (![object isKindOfClass:[ALFImport class]]) {
            return NO;
        }

        return [self isEqualToImport:(ALFImport *)object];
    }

    - (NSString *)description
    {
        return [NSString stringWithFormat:@"Import Name : %@",name];
    }

    @end


### 效果图
（无）

### 备注
（无）
