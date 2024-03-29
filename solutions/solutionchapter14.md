### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-02 | Alfred Jiang | - |

### 方案名称
NSString - 相似度检测

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
NSString \ 相似度 \ Damerau Levenshtein

### 需求场景
1. 需要计算两个字符串相似度的需求

### 参考链接
1. [OSChina](http://my.oschina.net/dourgulf/blog/60846)
2. [GitHub](https://github.com/JanX2/NSString-DamerauLevenshtein)

### 详细内容
1. 简单方案
        //
        //  NSString+Distance.m
        //  Levenshtein
        //
        //  Created by Dawen Rie on 12-6-4.
        //  Copyright (c) 2012年 G4 Workshop. All rights reserved.
        //

        #import "NSString+Distance.h"
        static inline int min(int a, int b) {
            return a < b ? a : b;
        }

        @implementation NSString (Distance)

        - (float) likePercent:(NSString *)target{
            int n = self.length;
            int m = target.length;
            if (m==0) return n;
            if (n==0) return m;

            //Construct a matrix, need C99 support
            int matrix[n+1][m+1];
            memset(&matrix[0], 0, m+1);
            for(int i=1; i<=n; i++) {
                memset(&matrix[i], 0, m+1);
                matrix[i][0]=i;
            }
            for(int i=1; i<=m; i++) {
                matrix[0][i]=i;
            }
            for(int i=1;i<=n;i++)
            {
                unichar si = [self characterAtIndex:i-1];
                for(int j=1;j<=m;j++)
                {
                    unichar dj = [target characterAtIndex:j-1];
                    int cost;
                    if(si==dj){
                        cost=0;
                    }
                    else{
                        cost=1;
                    }
                    const int above=matrix[i-1][j]+1;
                    const int left=matrix[i][j-1]+1;
                    const int diag=matrix[i-1][j-1]+cost;
                    matrix[i][j]=min(above,min(left,diag));
                }
            }
            return 100.0 - 100.0*matrix[n][m]/self.length;
        }

        @end

2. 专业方案（Damerau Levenshtein）[NSString-DamerauLevenshtein](https://github.com/JanX2/NSString-DamerauLevenshtein)
        NSString *test1 = @"I love Objective-C";
        NSString *test2 = @"I love Swift";

        NSLog(@"[test1 similarityToString:test2] : %f",[test1 similarityToString:test2]);
        NSLog(@"[test1 likePercent:test2] : %f",[test1 likePercent:test2]);

        //2015-03-02 16:37:23.077 TestButton[40210:436933] [test1 similarityToString:test2] : 0.444444
        //2015-03-02 16:37:23.078 TestButton[40210:436933] [test1 likePercent:test2] : 44.444443

### 效果图
（无）

### 备注
（无）
