### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-03 | Alfred Jiang | - |

### 方案名称
通知 - iOS7 的多任务处理——远程通知（Remote Notifications）

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
多任务处理 \ 后台获取数据 \ Background Fetch \ Remote Notification

### 需求场景
1. 需要获取服务器的通知时

### 参考链接
1. [iOS 7系列译文：iOS7的多任务处理](http://www.kuqin.com/shuoit/20131223/337138.html)

### 详细内容

1. 注册推送通知
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            [[UIApplication sharedApplication] registerForRemoteNotifications];
            return YES;
        }

2. 处理一个推送通知
        - (NSURLSession *)backgroundURLSession
        {
            static NSURLSession *session = nil;
            static dispatch_once_t onceToken;
            dispatch_once(&onceToken, ^{
            NSString *identifier = @"io.objc.backgroundTransferExample";
            NSURLSessionConfiguration* sessionConfig = [NSURLSessionConfiguration backgroundSessionConfiguration:identifier];
            session = [NSURLSession sessionWithConfiguration:sessionConfig
            delegate:self
            delegateQueue:[NSOperationQueue mainQueue]];
            });

            return session;
        }

        - (void) application:(UIApplication *)application
        didReceiveRemoteNotification:(NSDictionary *)userInfo
        fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"Received remote notification with userInfo %@", userInfo);
            NSNumber *contentID = userInfo[@"content-id"];
            NSString *downloadURLString = [NSString stringWithFormat:@"http://yourserver.com/downloads/%d.mp3", [contentID intValue]];
            NSURL* downloadURL = [NSURL URLWithString:downloadURLString];
            NSURLRequest *request = [NSURLRequest requestWithURL:downloadURL];
            NSURLSessionDownloadTask *task = [[self backgroundURLSession] downloadTaskWithRequest:request];
            task.taskDescription = [NSString stringWithFormat:@"Podcast Episode %d", [contentID intValue]];
            [task resume];
            completionHandler(UIBackgroundFetchResultNewData);
        }

3. 通过实现NSURLSessionDownloadDelegate的委托方法完成下载任务
        #Pragma Mark - NSURLSessionDownloadDelegate

        - (void) URLSession:(NSURLSession *)session
        downloadTask:(NSURLSessionDownloadTask *)downloadTask
        didFinishDownloadingToURL:(NSURL *)location
        {
            NSLog(@"downloadTask:%@ didFinishDownloadingToURL:%@", downloadTask.taskDescription, location);
            // Copy file to your app's storage with NSFileManager
            // ...
            // Notify your UI
        }

        - (void) URLSession:(NSURLSession *)session
        downloadTask:(NSURLSessionDownloadTask *)downloadTask
        didResumeAtOffset:(int64_t)fileOffset
        expectedTotalBytes:(int64_t)expectedTotalBytes
        {

        }

        - (void) URLSession:(NSURLSession *)session
        downloadTask:(NSURLSessionDownloadTask *)downloadTask
        didWriteData:(int64_t)bytesWritten totalBytesWritten:(int64_t)totalBytesWritten
        totalBytesExpectedToWrite:(int64_t)totalBytesExpectedToWrite
        {

        }

 任务完成下载时，你会得到一个磁盘上该文件的临时URL。你必须把这个文件移动或复制你的应用程序空间，因为当你从这个委托方法返回时，该文件将从临时存储中删除。

4. 当后台会话任务完成后，如果需要打开程序，则实现如下代码
        - (void) application:(UIApplication *)application
        handleEventsForBackgroundURLSession:(NSString *)identifier completionHandler:(void (^)())completionHandler
        {
            // You must re-establish a reference to the background session,
            // or NSURLSessionDownloadDelegate and NSURLSessionDelegate methods will not be called
            // as no delegate is attached to the session. See backgroundURLSession above.
            NSURLSession *backgroundSession = [self backgroundURLSession];
            NSLog(@"Rejoining session with identifier %@ %@", identifier, backgroundSession);
            // Store the completion handler to update your UI after processing session events
            [self addCompletionHandler:completionHandler forSession:identifier];
        }

        - (void)URLSessionDidFinishEventsForBackgroundURLSession:(NSURLSession *)session
        {
            NSLog(@"Background URL session %@ finished events.n", session);
            if (session.configuration.identifier) {
                // Call the handler we stored in -application:handleEventsForBackgroundURLSession:
                [self callCompletionHandlerForSession:session.configuration.identifier];
            }
        }

        - (void)addCompletionHandler:(CompletionHandlerType)handler forSession:(NSString *)identifier
        {
            if ([self.completionHandlerDictionary objectForKey:identifier]) {
                NSLog(@"Error: Got multiple handlers for a single session identifier. This should not happen.n");
            }
            [self.completionHandlerDictionary setObject:handler forKey:identifier];
        }

        - (void)callCompletionHandlerForSession: (NSString *)identifier
        {
            CompletionHandlerType handler = [self.completionHandlerDictionary objectForKey: identifier];
            if (handler) {
                [self.completionHandlerDictionary removeObjectForKey: identifier];
                NSLog(@"Calling completion handler for session %@", identifier);
                handler();
            }
        }
 不同于以往的委托回调，该应用程序委托会被调用两次，因为您的会话和任务委托可能会收到一系列消息。应用程序委托的：handleEventsForBackgroundURLSession：方法，在这些NSURLSession委托的消息发送前被调用，然后，URLSessionDidFinishEventsForBackgroundURLSession被调用。在前面的方法中，储存了一个后台完成处理代码（completionHandler），并在后面的方法中调用该代码更新界面。

### 效果图
（无）

### 备注
（无）
