### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-03 | Alfred Jiang | - |

### 问题描述
iOS App 开发常用设计模式有哪些

### 关键字
设计模式

### 参考链接
1. [IOS设计模式之一（MVC模式，单例模式）](http://xmuzyq.iteye.com/blog/1942376)(推荐译文)
2. [iOS Design Patterns](http://www.raywenderlich.com/46988/ios-design-patterns)
3. [Design-Patterns-In-Swift](https://github.com/ochococo/Design-Patterns-In-Swift)

### 解答参考
#####创建型：

######1. 单例模式（The Singleton Pattern）

各种sharedInstanceManager

#####结构型：

######1. 模型-视图-控制器（MVC）以及 MVVC

各种UIViewController

######2. 装饰器（The Decorator Design Pattern）

Category(类别）和Delegation（委托）

装饰器模式在不修改原来代码的情况下动态的给对象增加新的行为和职责，它通过一个对象包装被装饰对象的方法来修改类的行为，这种方法可以做为子类化的一种替代方法。
在Objective-C中，存在两种非常常见的实现:Category(类别）和Delegation（委托）。

######3. 适配器模式（The Adapter Pattern）

适配器可以让一些接口不兼容的类一起工作。它包装一个对象然后暴漏一个标准的交互接口。
如果你熟悉适配器设计模式，苹果通过一个稍微不同的方式来实现它-苹果使用了协议的方式来实现。你可能已经熟悉UITableViewDelegate, UIScrollViewDelegate, NSCoding 和 NSCopying协议。举个例子，使用NSCopying协议，任何类都可以提供一个标准的copy方法。

######4. 外观（门面）（The Facade Design Pattern）:

各种接口类

门面模式针对复杂的子系统提供了单一的接口，不需要暴漏一些列的类和API给用户，你仅仅暴漏一个简单统一的API。

#####行为型：

######1. 观察者模式（The Observer Pattern）

苹果的推送通知（Push Notification）

通知（Notifications）

Key-Value Observing(KVO)

######2. 备忘录模式（The Memento Pattern）

归档（Archiving）

备忘录模式快照对象的内部状态并将其保存到外部。换句话说，它将状态保存到某处，过会你可以不破坏封装的情况下恢复对象的状态，也就是说原来对象中的私有数据仍然是私有的。

    - (void)saveCurrentState
    {
        // When the user leaves the app and then comes back again, he wants it to be in the exact same state
        // he left it. In order to do this we need to save the currently displayed album.
        // Since it's only one piece of information we can use NSUserDefaults.
        [[NSUserDefaultsstandardUserDefaults] setInteger:currentAlbumIndex forKey:@"currentAlbumIndex"];
    }

    - (void)loadPreviousState
    {
        currentAlbumIndex = [[NSUserDefaultsstandardUserDefaults] integerForKey:@"currentAlbumIndex"];
        [self showDataForAlbumAtIndex:currentAlbumIndex];
    }

######3. 命令模式（The Command Pattern）

苹果通过Target-Action机制和Invocation实现命令模式。

命令模式将一个请求封装为一个对象。封装以后的请求会比原生的请求更加灵活，因为这些封装后的请求可以在多个对象之间传递，存储以便以后使用，还可以动态的修改，或者放进一个队列中


### 备注
