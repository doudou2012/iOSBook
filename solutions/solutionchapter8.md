### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-02 | Alfred Jiang | - |

### 方案名称
Block - 介绍与使用

### 方案类型（推荐 or 参考）
推荐

### 关键字
Block \ Dispatch \ GCD \ 异步 \ 并行

### 需求场景
1. 单例模式（dispatch_once）
2. 延时执行（dispatch_after）
3. 队列需求（dispatch_queue_create）
4. 孤立队列
5. 迭代执行
6. 组（dispatch_group_create）
7. 事件源
8. 输入输出
9. 基准测试
10. 原子操作
4. 动画
5. 回调
6. 异步
7. ......

### 参考链接
1. [CocoaChina](http://www.cocoachina.com/industry/20130821/6842.html)
2. [初探swift语言的学习笔记十](http://blog.csdn.net/fengsh998/article/details/35783341)

### 详细内容
#####1. 如何定义
1. Swift 解决方案
        // 申明
        //无参无返回值
        typealias funcBlock = () -> () //或者 () -> Void
        //返回值是String
        typealias funcBlockA = (Int,Int) -> String
        //返回值是一个函数指针，入参为String
        typealias funcBlockB = (Int,Int) -> (String)->()
        //返回值是一个函数指针，入参为String 返回值也是String
        typealias funcBlockC = (Int,Int) -> (String)->String

        //定义
         var blockProperty : funcBlockA = {a,b in return ""/**/} // 带初始化方式
         var blockPropertyNoReturn : (String) -> () = {param in }

        //调用
        //block作为函数参数
        func testBlock(blockfunc:funcBlock!)//使用!号不需要再解包
        {
            if let exsistblock = blockfunc
            {
                blockfunc() //无参无返回
            }
        }

2. Objective-C 解决方案
         // 申明
         (void) (^loggerBlock)(void);

         // 定义
         loggerBlock = ^{
              NSLog(@"Hello world");
         };

         // 调用
         loggerBlock();

#####2. 如何使用

(1) 异步API

如果你写一个类，让你类的使用这设置一个将回调传递到的队列会是一个好的选择。你的代码可能像这样
    - (void)processImage:(UIImage *)image completionHandler:(void(^)(BOOL success))handler;
    {
        dispatch_async(self.isolationQueue, ^(void){
            // do actual processing here
            dispatch_async(self.resultQueue, ^(void){
                handler(YES);
            });
        });
    }

也可能是这样

    // 原代码块一
    self.indicator.hidden = NO;
    [self.indicator startAnimating];
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        // 原代码块二
        NSURL * url = [NSURL URLWithString:@"http://www.youdao.com"];
        NSError * error;
        NSString * data = [NSString stringWithContentsOfURL:url encoding:NSUTF8StringEncoding error:&error];
        if (data != nil) {
            // 原代码块三
            dispatch_async(dispatch_get_main_queue(), ^{
                [self.indicator stopAnimating];
                self.indicator.hidden = YES;
                self.content.text = data;
            });
        } else {
            NSLog(@"error when download:%@", error);
        }
    });

Swift 示例

    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), { () -> Void in

        dispatch_async(dispatch_get_main_queue(), { () -> Void in

        })
    })

(2) 迭代执行

如果你的代码看起来是这样的：

    for (size_t y = 0; y < height; ++y) {
        for (size_t x = 0; x < width; ++x) {
            // Do something with x and y here
        }
    }

小小的改动，或许就可以让他运行的更快：

    dispatch_apply(height, dispatch_get_global_queue(0, 0), ^(size_t y) {
        for (size_t x = 0; x < width; x += 2) {
            // Do something with x and y here
        }
    });

(3) 并行执行的组

    dispatch_group_t group = dispatch_group_create();

    dispatch_queue_t queue = dispatch_get_global_queue(0, 0);
    dispatch_group_async(group, queue, ^(){
        // Do something that takes a while
        [self doSomeFoo];
        dispatch_group_async(group, dispatch_get_main_queue(), ^(){
            self.foo = 42;
        });
    });

    dispatch_group_async(group, queue,^(){
        // Do something else that takes a while
        [self doSomeBar];
        dispatch_group_async(group, dispatch_get_main_queue(), ^(){
            self.bar = 1;
        });
    });

    // This block will run once everything above is done:
    dispatch_group_notify(group, dispatch_get_main_queue(), ^(){
        NSLog(@"foo: %ld", (long)self.foo);
        NSLog(@"bar: %ld", (long)self.bar);
    });

    //2015-03-02 13:44:22.961 TestButton[34856:267105] foo: 42
    //2015-03-02 13:44:22.962 TestButton[34856:267105] bar: 1

(4) 对现有API使用dispatch_group_t

一旦你将组作为你的工具箱中的一部分，你可能会想知道为什么大多数的异步API不把dispatch_group_t作为其的一个可选参数。这没有什么令人绝望的理由，仅仅是因为自己添加这个功能太简单了，但是你还是要小心以确保自己的代码是成对出现的。

举例来说，我们可以给Core Data的-performBlock:函数添加上组的功能，那么API会变得像这个样子：
    - (void)withGroup:(dispatch_group_t)group performBlock:(dispatch_block_t)block
    {
        if (group == NULL) {
            [self performBlock:block];
        } else {
            dispatch_group_enter(group);
            [self performBlock:^(){
                block();
                dispatch_group_leave(group);
            }];
        }
    }

这样做允许我们使用dispatch_group_notify来运行一段代码，当Core Data上的一堆操作完成以后。
很明显，我们可以给NSURLConnection做同样的事情：
    + (void)withGroup:(dispatch_group_t)group
            sendAsynchronousRequest:(NSURLRequest *)request
            queue:(NSOperationQueue *)queue
            completionHandler:(void (^)(NSURLResponse*, NSData*, NSError*))handler
    {
        if (group == NULL) {
            [self sendAsynchronousRequest:request
                                    queue:queue
                        completionHandler:handler];
        } else {
            dispatch_group_enter(group);
            [self sendAsynchronousRequest:request
                                    queue:queue
                        completionHandler:^(NSURLResponse *response, NSData *data, NSError *error){
                handler(response, data, error);
                dispatch_group_leave(group);
            }];
        }
    }

为了能正常工作，你需要确保:

dispatch_group_enter()一定要在dispatch_group_leave()之前运行。

dispatch_group_enter()和dispatch_group_leave()通常是成对出现的（就算有错误产生时）。

(5) 监视进程

如果一些进程正在运行而你想知道他们什么时候存在，GCD能够做到这些。你也可以使用GCD来检测进程什么时候分叉，也就是产生了子进程或者一个信号被传送给了进程（比如SIGTERM）。

    NSRunningApplication *mail = [NSRunningApplication
      runningApplicationsWithBundleIdentifier:@"com.apple.mail"];
    if (mail == nil) {
        return;
    }
    pid_t const pid = mail.processIdentifier;
    self.source = dispatch_source_create(DISPATCH_SOURCE_TYPE_PROC, pid,
      DISPATCH_PROC_EXIT, DISPATCH_TARGET_QUEUE_DEFAULT);
    dispatch_source_set_event_handler(self.source, ^(){
        NSLog(@"Mail quit.");
    });
    dispatch_resume(self.source);

当Mail.app退出的时候，这个程序会打印出Mail quit.。

### 效果图
（无）

### 备注

1. block 在实现时就会对它引用到的它所在方法中定义的栈变量进行一次只读拷贝，然后在 block 块内使用该只读拷贝；

2. 非内联（inline） block 不能直接访问 self，只能通过将 self 当作参数传递到 block 中才能使用，并且此时的 self 只能通过 setter 或 getter 方法访问其属性，不能使用句点式方法。但内联 block 不受此限制；

3. 使用 weak–strong dance 技术来避免循环引用；

4. block 内存管理分析：
block 其实也是一个 NSObject 对象，并且在大多数情况下，block 是分配在栈上面的，只有当 block 被定义为全局变量或 block 块中没有引用任何 automatic 变量时，block 才分配在全局数据段上。 __block 变量也是分配在栈上面的。
