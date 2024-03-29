### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-26 | Alfred Jiang | - |

### 方案名称
语法 - 随机数生成实现

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
随机ID \ 随机字符串 \ 随机数

### 需求场景
1. 测试随机结果的场景需求

### 参考链接
1. [ios 生成不重复随机数](http://blog.csdn.net/jiajiayouba/article/details/43450621)

### 详细内容
ios 有如下三种随机数方法：

    //1.
    srand((unsigned)time(0));  //不加这句每次产生的随机数不变
    int i = rand() % 5;

    //2.
    srandom(time(0));
    int i = random() % 5;

    //3.
    int i = arc4random() % 5 ;



注：rand()和random()实际并不是一个真正的伪随机数发生器，在使用之前需要先初始化随机种子，否则每次生成的随机数一样。

arc4random() 是一个真正的伪随机算法，不需要生成随机种子，因为第一次调用的时候就会自动生成。而且范围是rand()的两倍。在iPhone中，RAND_MAX是0x7fffffff (2147483647)，而arc4random()返回的最大值则是 0x100000000 (4294967296)。

精确度比较：arc4random()  >  random()  >  rand()。

常用方法：arc4random

1. 获取一个随机整数范围在：[0,100)包括0，不包括100

        int x = arc4random() % 100;

2. 获取一个随机数范围在：[500,1000），包括500，不包括1000

        int y = (arc4random() % 501) + 500;

3. 获取一个随机整数，范围在[from,to），包括from，不包括to

        -(int)getRandomNumber:(int)from to:(int)to
        {
            return (int)(from + (arc4random() % (to – from + 1)));
        }

### 效果图
（无）

### 备注
（无）
