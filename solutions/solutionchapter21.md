### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-03 | Alfred Jiang | - |

### 方案名称
设计模式 - 使用命令模式实现撤销删除

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
命令模式 \ 设计模式 \ 撤销删除

### 需求场景
1. 较为复杂的撤销删除需求

### 参考链接
1. [IOS设计模式之四（备忘录模式，命令模式）](http://xmuzyq.iteye.com/blog/1942386)

### 详细内容

命令模式将一个请求封装为一个对象。封装以后的请求会比原生的请求更加灵活，因为这些封装后的请求可以在多个对象之间传递，存储以便以后使用，还可以动态的修改，或者放进一个队列中。苹果通过Target-Action机制和Invocation实现命令模式。

    - (void)deleteAlbum
    {
        // 1
        Album *deletedAlbum = allAlbums[currentAlbumIndex];

        // 2
        NSMethodSignature *sig = [self methodSignatureForSelector:@selector(addAlbum:atIndex:)];
        NSInvocation *undoAction = [NSInvocationinvocationWithMethodSignature:sig];
        [undoAction setTarget:self];
        [undoAction setSelector:@selector(addAlbum:atIndex:)];
        [undoAction setArgument:&deletedAlbum atIndex:2];
        [undoAction setArgument:&currentAlbumIndex atIndex:3];
        [undoAction retainArguments];

        // 3
        [undoStack addObject:undoAction];

        // 4
        [[LibraryAPI sharedInstance] deleteAlbumAtIndex:currentAlbumIndex];
        [self reloadScroller];

        // 5
        [toolbar.items[0] setEnabled:YES];
    }

上面的代码中有一些新的激动人心的特性，所以下面我们就来考虑每个被标注了注释的地方：
    1. 获取需要删除的专辑

    2. 定义了一个类型为NSMethodSignature的对象去创建NSInvocation,它将用来撤销删除操作。NSInvocation需要知道三件事情：选择器（发送什么消息），目标对象（发送消息的对象），还有就是消息所需要的参数。在上面的例子中，消息是与删除方法相反的操作，因为当你想撤销删除的时候，你需要将刚删除的数据回加回去。

    3. 创建了undoAction以后，你需要将其增加到undoStack中。撤销操作将被增加在数组的末尾。

    4. 使用LibraryAPI删除专辑,然后重新加载滚动视图。

    5. 因为在撤销栈中已经有了操作，你需要使得撤销按钮可用。

注意：使用NSInvocation，你需要记住下面的几点：

    1.参数必须以指针的形式传递.

    2.参数从索引2开始，索引0，1为目标（target）和选择器（selector）保留。

    3.如果参数有可能会被销毁，你需要调用retainArguments.

撤销方法：

    - (void)undoAction
    {
        if (undoStack.count > 0)
        {
            NSInvocation *undoAction = [undoStack lastObject];
            [undoStack removeLastObject];
            [undoAction invoke];
        }

        if (undoStack.count == 0)
        {
            [toolbar.items[0] setEnabled:NO];
        }
    }


### 效果图
（无）

### 备注
（无）
