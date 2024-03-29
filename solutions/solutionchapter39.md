### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-18 | Alfred Jiang | - |

### 方案名称
网络 - 判断连接状态

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
网络连接 \ Network \ Connection

### 需求场景
1. 需要对网络状态进行判断的场景

### 参考链接
（无）

### 详细内容

#####1. 导入SystemConfiguration.framework,并#import<SystemConfiguration/SCNetworkReachability.h>

#####2. 判断设备是否联网

    + (BOOL)connectedToNetwork{

        //创建零地址，0.0.0.0的地址表示查询本机的网络连接状态

        struct sockaddr_storage zeroAddress;

        bzero(&zeroAddress, sizeof(zeroAddress));
        zeroAddress.ss_len = sizeof(zeroAddress);
        zeroAddress.ss_family = AF_INET;

        // Recover reachability flags
        SCNetworkReachabilityRef defaultRouteReachability = SCNetworkReachabilityCreateWithAddress(NULL, (struct sockaddr *)&zeroAddress);
        SCNetworkReachabilityFlags flags;

        //获得连接的标志
        BOOL didRetrieveFlags = SCNetworkReachabilityGetFlags(defaultRouteReachability, &flags);
        CFRelease(defaultRouteReachability);

        //如果不能获取连接标志，则不能连接网络，直接返回
        if (!didRetrieveFlags)
        {
            return NO;
        }
        //根据获得的连接标志进行判断

        BOOL isReachable = flags & kSCNetworkFlagsReachable;
        BOOL needsConnection = flags & kSCNetworkFlagsConnectionRequired;
        return (isReachable&&!needsConnection) ? YES : NO;
    }

### 效果图
（无）

### 备注
（无）
