### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-26 | Alfred Jiang | 录入 |
| 2 | 2015-08-18 | Alfred Jiang | 更新 |

### 方案名称
NSString - 汉字繁体简体相互转换的实现

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
简体汉字 \ 繁体汉字 \ 繁简转换

### 需求场景
1. 需要对本地工程显示文字进行繁体和简体互相转换时

### 参考链接
1. [GitHub : OBCConvertor](https://github.com/so898/OBCConvertor)
2. [GitHub : ChineseToPinYin](https://github.com/willonboy/ChineseToPinYin/tree/9cce145a85b56bb6893ef88fd64c911379f266a4)

### 详细内容

#####1. convertGB_BIG

需要添加 big5.txt 和 gb.txt 文件

1. convertGB_BIG.h
        //
        //  convertGB_BIG.h
        //  myTest
        //
        //  Created by sffofn on 11-8-17.
        //  Copyright 2011 keke.com. All rights reserved.
        //

        #import <Foundation/Foundation.h>

        @interface convertGB_BIG : NSObject {
        	NSString*	_string_GB;
        	NSString*	_string_BIG5;
        }

        @property(nonatomic, retain) NSString*	string_GB;
        @property(nonatomic, retain) NSString*	string_BIG5;

        -(NSString*)gbToBig5:(NSString*)srcString;
        -(NSString*)big5ToGb:(NSString*)srcString;

        @end

        1. convertGB_BIG.h
        //
        //  convertGB_BIG.h
        //  myTest
        //
        //  Created by sffofn on 11-8-17.
        //  Copyright 2011 keke.com. All rights reserved.
        //

        #import <Foundation/Foundation.h>

        @interface convertGB_BIG : NSObject {
        	NSString*	_string_GB;
        	NSString*	_string_BIG5;
        }

        @property(nonatomic, retain) NSString*	string_GB;
        @property(nonatomic, retain) NSString*	string_BIG5;

        -(NSString*)gbToBig5:(NSString*)srcString;
        -(NSString*)big5ToGb:(NSString*)srcString;

        @end

2. convertGB_BIG.m
        //
        //  convertGB_BIG.m
        //  myTest
        //
        //  Created by sffofn on 11-8-17.
        //  Copyright 2011 keke.com. All rights reserved.
        //

        #import "convertGB_BIG.h"

        @implementation convertGB_BIG
        @synthesize string_GB = _string_GB;
        @synthesize string_BIG5 = _string_BIG5;

        -(void)dealloc
        {
        	[_string_GB release];
        	[_string_BIG5 release];

        	[super dealloc];
        }

        -(id)init
        {
        	if(self = [super init])
        	{
        		NSError *error;
        		NSString *resourcePath = [ [NSBundle mainBundle] resourcePath];
        		self.string_GB = [NSString stringWithContentsOfFile:[resourcePath stringByAppendingPathComponent:@"gb.txt"]
        												   encoding:NSUTF8StringEncoding
        													  error:&error];
        		self.string_BIG5 = [NSString stringWithContentsOfFile:[resourcePath stringByAppendingPathComponent:@"big5.txt"]
        												   encoding:NSUTF8StringEncoding
        													  error:&error];
        	}

        	return self;
        }

        //简体转繁体
        -(NSString*)gbToBig5:(NSString*)srcString
        {
        	NSInteger length = [srcString length];
        	for (NSInteger i = 0; i< length; i++)
        	{
        		NSString *string = [srcString substringWithRange:NSMakeRange(i, 1)];
        		NSRange gbRange = [_string_GB rangeOfString:string];
        		if(gbRange.location != NSNotFound)
        		{
        			NSString *big5String = [_string_BIG5 substringWithRange:gbRange];
        			srcString = [srcString stringByReplacingCharactersInRange:NSMakeRange(i, 1)
        											   withString:big5String];
        		}
        	}

        	return srcString;
        }

        //繁体转简体
        -(NSString*)big5ToGb:(NSString*)srcString
        {
        	NSInteger length = [srcString length];
        	for (NSInteger i = 0; i< length; i++)
        	{
        		NSString *string = [srcString substringWithRange:NSMakeRange(i, 1)];
        		NSRange big5Range = [_string_BIG5 rangeOfString:string];
        		if(big5Range.location != NSNotFound)
        		{
        			NSString *gbString = [_string_GB substringWithRange:big5Range];
        			srcString = [srcString stringByReplacingCharactersInRange:NSMakeRange(i, 1)
        											   withString:gbString];
        		}
        	}

        	return srcString;
        }

        @end

#####2. OBCConvertor

需要添加 ts.tab 文件

1. OBCConvertor.h
        //
        //  OBCConvertor.h
        //
        //  Created by Bill Cheng on 11/1/13.
        //  Copyright 2013 R3 Studio. All rights reserved.
        //
        //  Permission is hereby granted, free of charge, to any person obtaining a copy
        //  of this software and associated documentation files (the "Software"), to deal
        //  in the Software without restriction, including without limitation the rights
        //  to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
        //  copies of the Software, and to permit persons to whom the Software is
        //  furnished to do so, subject to the following conditions:
        //
        //  The above copyright notice and this permission notice shall be included in
        //  all copies or substantial portions of the Software.
        //
        //  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
        //  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
        //  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
        //  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
        //  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
        //  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
        //  THE SOFTWARE.
        //

        #import <Foundation/Foundation.h>

        @interface OBCConvertor : NSObject{
            NSMutableDictionary *ts, *st;
        }

        // Create Instance for the class
        + (OBCConvertor*)getInstance;

        // Convert Tradictional chinese to Simple chinese
        - (NSString*)t2s:(NSString*)string;

        // convert Simple chinese to Tradictional chinese
        - (NSString*)s2t:(NSString*)string;

        @end

2. OBCConvertor.m
        //
        //  OBCConvertor.m
        //
        //  Created by Bill Cheng on 11/1/13.
        //  Copyright 2013 R3 Studio. All rights reserved.
        //
        //  Permission is hereby granted, free of charge, to any person obtaining a copy
        //  of this software and associated documentation files (the "Software"), to deal
        //  in the Software without restriction, including without limitation the rights
        //  to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
        //  copies of the Software, and to permit persons to whom the Software is
        //  furnished to do so, subject to the following conditions:
        //
        //  The above copyright notice and this permission notice shall be included in
        //  all copies or substantial portions of the Software.
        //
        //  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
        //  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
        //  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
        //  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
        //  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
        //  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
        //  THE SOFTWARE.
        //

        #import "OBCConvertor.h"

        static OBCConvertor * instance=nil;

        @implementation OBCConvertor

        + (OBCConvertor*)getInstance
        {
            @synchronized(self) {
                if (instance==nil) {
                    instance=[[OBCConvertor alloc] init];
                }
            }
            return instance;
        }

        - (id)init
        {
            self = [super init];
            if (self) {
                // Initialize self.
                // Read code table from ts.tab file
                NSString *filrPath = [[NSBundle mainBundle] pathForResource:@"ts.tab" ofType:nil];
                NSString *data = [NSString stringWithContentsOfFile:filrPath encoding:NSUTF8StringEncoding error:NULL];

                // Change the NSString to Char
                NSMutableArray *chars = [[NSMutableArray alloc] initWithCapacity:[data length]];
                for (int i=0; i < [data length]; i++) {
                    NSString *ichar  = [NSString stringWithFormat:@"%C", [data characterAtIndex:i]];
                    [chars addObject:ichar];
                }

                ts = [NSMutableDictionary new];
                st = [NSMutableDictionary new];

                for (int i = 0; i < [chars count] ; i = i + 2){
                    NSString *one = [chars objectAtIndex:i];
                    NSString *two = [chars objectAtIndex:(i + 1)];
                    [st setObject:one forKey:two];
                    [ts setObject:two forKey:one];
                }
            }
            return self;
        }

        - (NSString*)t2s:(NSString*)string;
        {
            NSString *result = @"";
            NSMutableArray *tmpArray = [[NSMutableArray alloc] initWithCapacity:[string length]];
            for (int i=0; i < [string length]; i++) {
                NSString *ichar  = [NSString stringWithFormat:@"%C", [string characterAtIndex:i]];
                [tmpArray addObject:ichar];
            }
            for (NSString *s in tmpArray){
                if ([ts objectForKey:s]){
                    result = [NSString stringWithFormat:@"%@%@", result, [ts objectForKey:s]];
                } else {
                    result = [NSString stringWithFormat:@"%@%@", result, s];
                }
            }
            [tmpArray release];
            return result;
        }

        - (NSString*)s2t:(NSString*)string
        {
            NSString *result = @"";
            NSMutableArray *tmpArray = [[NSMutableArray alloc] initWithCapacity:[string length]];
            for (int i=0; i < [string length]; i++) {
                NSString *ichar  = [NSString stringWithFormat:@"%C", [string characterAtIndex:i]];
                [tmpArray addObject:ichar];
            }
            for (NSString *s in tmpArray){
                if ([st objectForKey:s]){
                    result = [NSString stringWithFormat:@"%@%@", result, [st objectForKey:s]];
                } else {
                    result = [NSString stringWithFormat:@"%@%@", result, s];
                }
            }
            [tmpArray release];
            return result;
        }

        @end


### 效果图
（无）

### 备注
（无）
