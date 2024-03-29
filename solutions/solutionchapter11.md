### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-02 | Alfred Jiang | - |

### 方案名称
数据持久化 - 序列化对象

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
序列化对象 \ NSKeyedArchiver \ NSJSONSeriali \ 文件存储

### 需求场景
1. 需要文件存储对象数据时
2. 部分序列化对象需求

### 参考链接
1. [GitHub](https://github.com/randomsequence/NSSerialisationTests)

### 详细内容
通过NSKeyedArchiver和NSJSONSeriali的对比测试说明序列化对象的一些基本操作和用途

1. Model.h
        //
        //  Model.h
        //  Serialisation Test
        //
        //  Created by Johnnie Walker on 09/05/2013.
        //  Copyright (c) 2013 Random Sequence. All rights reserved.
        //

        #import <Foundation/Foundation.h>

        @interface Model : NSObject <NSCoding>
        @property (nonatomic) NSRect rect;
        @property (nonatomic) NSPoint point;
        @property (nonatomic, copy) NSString *UUID;

        + (id)model;
        - (NSDictionary *)dict;
        + (id)modelWithDictionary:(NSDictionary *)dictionary;

        @end
2. Model.m
        //
        //  Model.m
        //  Serialisation Test
        //
        //  Created by Johnnie Walker on 09/05/2013.
        //  Copyright (c) 2013 Random Sequence. All rights reserved.
        //

        #import "Model.h"

        NSString * const UUIDKey = @"UUID";
        NSString * const PointKey = @"point";
        NSString * const RectKey = @"rect";

        @implementation Model

        + (id)model {
            Model *model = [Model new];
            model.UUID = [[NSUUID UUID] UUIDString];
            model.point = NSMakePoint(arc4random(), arc4random());
            model.rect = NSMakeRect(model.point.x, model.point.y, arc4random(), arc4random());
            return model;
        }

        - (void)encodeWithCoder:(NSCoder *)aCoder {
            if ([aCoder allowsKeyedCoding]) {
                [aCoder encodeObject:self.UUID forKey:UUIDKey];
                [aCoder encodePoint:self.point forKey:PointKey];
                [aCoder encodeRect:self.rect forKey:RectKey];
            } else {
                [aCoder encodeObject:self.UUID];
                [aCoder encodePoint:self.point];
                [aCoder encodeRect:self.rect];
            }
        }

        - (id)initWithCoder:(NSCoder *)decoder {
            self = [super init];
            if (self) {
                if ([decoder allowsKeyedCoding]) {
                    self.UUID = [decoder decodeObjectForKey:UUIDKey];
                    self.point = [decoder decodePointForKey:PointKey];
                    self.rect = [decoder decodeRectForKey:RectKey];
                } else {
                    self.UUID = [decoder decodeObject];
                    self.point = [decoder decodePoint];
                    self.rect = [decoder decodeRect];
                }
            }
            return self;
        }

        - (NSDictionary *)dict {
            return @{
                     UUIDKey: self.UUID,
                     PointKey: NSStringFromPoint(self.point),
                     RectKey: NSStringFromRect(self.rect)
                     };
        }

        + (id)modelWithDictionary:(NSDictionary *)dictionary {
            Model *model = [Model new];
            model.UUID = [dictionary objectForKey:UUIDKey];
            model.point = NSPointFromString([dictionary objectForKey:PointKey]);
            model.rect = NSRectFromString([dictionary objectForKey:RectKey]);
            return model;
        }

        @end

3. Serialisation_TestTests.m
        //
        //  Serialisation_TestTests.m
        //  Serialisation TestTests
        //
        //  Created by Johnnie Walker on 09/05/2013.
        //  Copyright (c) 2013 Random Sequence. All rights reserved.
        //

        #import "Serialisation_TestTests.h"
        #import "Model.h"

        #define ITERATIONS 100000

        @interface Serialisation_TestTests ()
        @property (nonatomic, strong) NSArray *models;
        @end

        @implementation Serialisation_TestTests

        - (void)setUp {
            NSMutableArray *models = [NSMutableArray arrayWithCapacity:ITERATIONS];
            for (NSInteger i=0; i<ITERATIONS; i++) {
                [models addObject:[Model model]];
            }
            self.models = models;
        }

        - (void)tearDown {
            self.models = nil;
        }

        - (void)testNSCoding
        {
            NSDate *startDate = [NSDate date];
            NSMutableArray *array = [NSMutableArray arrayWithCapacity:ITERATIONS];
            for (Model *model in self.models) {
                [array addObject:[NSArchiver archivedDataWithRootObject:model]];
            }
            NSMutableArray *models1 = [NSMutableArray arrayWithCapacity:ITERATIONS];
            for (NSData *data in array) {
                [models1 addObject:[NSUnarchiver unarchiveObjectWithData:data]];
            }
            NSDate *endDate = [NSDate date];

            NSLog(@"NSArchiver models: %li duration: %f", (unsigned long)[models1 count], [endDate timeIntervalSinceDate:startDate]);
        }

        - (void)testNSKeyedCoding
        {
            NSDate *startDate = [NSDate date];
            NSMutableArray *array = [NSMutableArray arrayWithCapacity:ITERATIONS];
            for (Model *model in self.models) {
                [array addObject:[NSKeyedArchiver archivedDataWithRootObject:model]];
            }
            NSMutableArray *models1 = [NSMutableArray arrayWithCapacity:ITERATIONS];
            for (NSData *data in array) {
                [models1 addObject:[NSKeyedUnarchiver unarchiveObjectWithData:data]];
            }
            NSDate *endDate = [NSDate date];

            NSLog(@"NSKeyedArchiver models: %li duration: %f", (unsigned long)[models1 count], [endDate timeIntervalSinceDate:startDate]);
        }

        - (void)testPropertyListSerialisation
        {
            NSDate *startDate = [NSDate date];
            NSMutableArray *array = [NSMutableArray arrayWithCapacity:ITERATIONS];
            for (Model *model in self.models) {
                [array addObject:[NSPropertyListSerialization dataWithPropertyList:[model dict] format:NSPropertyListBinaryFormat_v1_0 options:0 error:nil]];
            }
            NSMutableArray *models1 = [NSMutableArray arrayWithCapacity:ITERATIONS];
            for (NSData *data in array) {
                [models1 addObject:
                 [Model modelWithDictionary:
                  [NSPropertyListSerialization propertyListWithData:data
                                                            options:NSPropertyListImmutable
                                                             format:NULL
                                                              error:nil]]];
            }
            NSDate *endDate = [NSDate date];

            NSLog(@"NSPropertyListSerialization models: %li duration: %f", (unsigned long)[models1 count], [endDate timeIntervalSinceDate:startDate]);
        }

        - (void)testJSONSerialisation
        {
            NSDate *startDate = [NSDate date];
            NSMutableArray *array = [NSMutableArray arrayWithCapacity:ITERATIONS];
            for (Model *model in self.models) {
                [array addObject:[NSJSONSerialization dataWithJSONObject:[model dict] options:0 error:nil]];
            }
            NSMutableArray *models1 = [NSMutableArray arrayWithCapacity:ITERATIONS];
            for (NSData *data in array) {
                [models1 addObject:[Model modelWithDictionary:[NSJSONSerialization JSONObjectWithData:data options:0 error:nil]]];
            }
            NSDate *endDate = [NSDate date];

            NSLog(@"NSJSONSerialization models: %li duration: %f", (unsigned long)[models1 count], [endDate timeIntervalSinceDate:startDate]);
        }

        @end

4. 执行测试结果
        Test Case '-[Serialisation_TestTests testJSONSerialisation]' started.
        2015-03-02 15:02:50.335 Serialisation Test[37221:303] NSJSONSerialization models: 100000 duration: 2.247854
        Test Case '-[Serialisation_TestTests testJSONSerialisation]' passed (2.516 seconds).
        Test Case '-[Serialisation_TestTests testNSCoding]' started.
        2015-03-02 15:02:53.854 Serialisation Test[37221:303] NSArchiver models: 100000 duration: 2.789287
        Test Case '-[Serialisation_TestTests testNSCoding]' passed (2.967 seconds).
        Test Case '-[Serialisation_TestTests testNSKeyedCoding]' started.
        2015-03-02 15:03:01.146 Serialisation Test[37221:303] NSKeyedArchiver models: 100000 duration: 6.989706
        Test Case '-[Serialisation_TestTests testNSKeyedCoding]' passed (7.217 seconds).
        Test Case '-[Serialisation_TestTests testPropertyListSerialisation]' started.
        2015-03-02 15:03:04.015 Serialisation Test[37221:303] NSPropertyListSerialization models: 100000 duration: 2.544815
        Test Case '-[Serialisation_TestTests testPropertyListSerialisation]' passed (2.787 seconds).
        Test Suite 'Serialisation_TestTests' finished at 2015-03-02 07:03:04 +0000.
        Executed 4 tests, with 0 failures (0 unexpected) in 15.488 (16.354) seconds
        Test Suite '/Users/ajiang048/Library/Developer/Xcode/DerivedData/Serialisation_Test-betqttfwfsrkofaxdppxjckonkic/Build/Products/Debug/Serialisation TestTests.octest(Tests)' finished at 2015-03-02 07:03:04 +0000.
        Executed 4 tests, with 0 failures (0 unexpected) in 15.488 (16.354) seconds
        Test Suite 'All tests' finished at 2015-03-02 07:03:04 +0000.
        Executed 4 tests, with 0 failures (0 unexpected) in 15.488 (16.356) seconds


### 效果图
（无）

### 备注
NSJSONSerialization 比 NSKeyedArchiver 在速度更快，而且序列化之后的体积更小，推荐优先使用 NSJSONSerialization 方式进行序列化操作
