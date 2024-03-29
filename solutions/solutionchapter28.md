### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-05 | Alfred Jiang | - |

### 方案名称
网络 - 使用 AFNetworking 实现网络请求

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
AFNetworking \ 网络编程 \ 网络请求

### 需求场景
1. 需要实现网络交互的需求时

### 参考链接
1. [GitHub](https://github.com/AFNetworking/AFNetworking)
2. [CSDN](http://blog.csdn.net/alfred_kwong/article/details/21176135)
3. [AFNetworking](http://afnetworking.com/)
4. [AFNetworking2.0源码解析](http://blog.cnbang.net/tech/2320/)

### 详细内容
#####1. 下载 [AFNetworking](http://afnetworking.com/) 并添加入工程

直接在 Prefix.pch 文件中引入，或者在工程的网络管理模块相关文件中引入。

#####2. 快速创建各种请求
1. Get 请求
        AFHTTPRequestOperationManager *manager = [AFHTTPRequestOperationManager manager];
        [manager GET:@"http://example.com/resources.json" parameters:nil success:^(AFHTTPRequestOperation *operation, id responseObject) {
            NSLog(@"JSON: %@", responseObject);
        } failure:^(AFHTTPRequestOperation *operation, NSError *error) {
            NSLog(@"Error: %@", error);
        }];

2. Post 表格数据
        AFHTTPRequestOperationManager *manager = [AFHTTPRequestOperationManager manager];
        NSDictionary *parameters = @{@"foo": @"bar"};
        [manager POST:@"http://example.com/resources.json" parameters:parameters success:^(AFHTTPRequestOperation *operation, id responseObject) {
            NSLog(@"JSON: %@", responseObject);
        } failure:^(AFHTTPRequestOperation *operation, NSError *error) {
            NSLog(@"Error: %@", error);
        }];

3. Post 文件
        AFHTTPRequestOperationManager *manager = [AFHTTPRequestOperationManager manager];
        NSDictionary *parameters = @{@"foo": @"bar"};
        NSURL *filePath = [NSURL fileURLWithPath:@"file://path/to/image.png"];
        [manager POST:@"http://example.com/resources.json" parameters:parameters constructingBodyWithBlock:^(id<AFMultipartFormData> formData) {
            [formData appendPartWithFileURL:filePath name:@"image" error:nil];
        } success:^(AFHTTPRequestOperation *operation, id responseObject) {
            NSLog(@"Success: %@", responseObject);
        } failure:^(AFHTTPRequestOperation *operation, NSError *error) {
            NSLog(@"Error: %@", error);
        }];

4. 创建下载任务
        NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
        AFURLSessionManager *manager = [[AFURLSessionManager alloc] initWithSessionConfiguration:configuration];

        NSURL *URL = [NSURL URLWithString:@"http://example.com/download.zip"];
        NSURLRequest *request = [NSURLRequest requestWithURL:URL];

        NSURLSessionDownloadTask *downloadTask = [manager downloadTaskWithRequest:request progress:nil destination:^NSURL *(NSURL *targetPath, NSURLResponse *response) {
            NSURL *documentsDirectoryURL = [[NSFileManager defaultManager] URLForDirectory:NSDocumentDirectory inDomain:NSUserDomainMask appropriateForURL:nil create:NO error:nil];
            return [documentsDirectoryURL URLByAppendingPathComponent:[response suggestedFilename]];
        } completionHandler:^(NSURLResponse *response, NSURL *filePath, NSError *error) {
            NSLog(@"File downloaded to: %@", filePath);
        }];
        [downloadTask resume];

5. 创建上传任务
        NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
        AFURLSessionManager *manager = [[AFURLSessionManager alloc] initWithSessionConfiguration:configuration];

        NSURL *URL = [NSURL URLWithString:@"http://example.com/upload"];
        NSURLRequest *request = [NSURLRequest requestWithURL:URL];

        NSURL *filePath = [NSURL fileURLWithPath:@"file://path/to/image.png"];
        NSURLSessionUploadTask *uploadTask = [manager uploadTaskWithRequest:request fromFile:filePath progress:nil completionHandler:^(NSURLResponse *response, id responseObject, NSError *error) {
            if (error) {
                NSLog(@"Error: %@", error);
            } else {
                NSLog(@"Success: %@ %@", response, responseObject);
            }
        }];
        [uploadTask resume];

6. 创建多文件上传，并显示进度
        NSMutableURLRequest *request = [[AFHTTPRequestSerializer serializer] multipartFormRequestWithMethod:@"POST" URLString:@"http://example.com/upload" parameters:nil constructingBodyWithBlock:^(id<AFMultipartFormData> formData) {
                [formData appendPartWithFileURL:[NSURL fileURLWithPath:@"file://path/to/image.jpg"] name:@"file" fileName:@"filename.jpg" mimeType:@"image/jpeg" error:nil];
            } error:nil];

        AFURLSessionManager *manager = [[AFURLSessionManager alloc] initWithSessionConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]];
        NSProgress *progress = nil;

        NSURLSessionUploadTask *uploadTask = [manager uploadTaskWithStreamedRequest:request progress:&progress completionHandler:^(NSURLResponse *response, id responseObject, NSError *error) {
            if (error) {
                NSLog(@"Error: %@", error);
            } else {
                NSLog(@"%@ %@", response, responseObject);
            }
        }];

        [uploadTask resume];

7. 创建一个 Data 的上传下载任务
        NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
        AFURLSessionManager *manager = [[AFURLSessionManager alloc] initWithSessionConfiguration:configuration];

        NSURL *URL = [NSURL URLWithString:@"http://example.com/upload"];
        NSURLRequest *request = [NSURLRequest requestWithURL:URL];

        NSURLSessionDataTask *dataTask = [manager dataTaskWithRequest:request completionHandler:^(NSURLResponse *response, id responseObject, NSError *error) {
            if (error) {
                NSLog(@"Error: %@", error);
            } else {
                NSLog(@"%@ %@", response, responseObject);
            }
        }];
        [dataTask resume];

#####3. 网络状态监测
        [[AFNetworkReachabilityManager sharedManager] setReachabilityStatusChangeBlock:^(AFNetworkReachabilityStatus status) {
            NSLog(@"Reachability: %@", AFStringFromNetworkReachabilityStatus(status));
        }];

#####4. 对 UIKit 的功能扩展

UIKit+AFNetworking.h

    #import <UIKit/UIKit.h>

    #ifndef _UIKIT_AFNETWORKING_
        #define _UIKIT_AFNETWORKING_

        #import "AFNetworkActivityIndicatorManager.h"

        #import "UIActivityIndicatorView+AFNetworking.h"
        #import "UIAlertView+AFNetworking.h"
        #import "UIButton+AFNetworking.h"
        #import "UIImageView+AFNetworking.h"
        #import "UIKit+AFNetworking.h"
        #import "UIProgressView+AFNetworking.h"
        #import "UIWebView+AFNetworking.h"
    #endif /* _UIKIT_AFNETWORKING_ */


### 效果图
（无）

### 备注
（无）
