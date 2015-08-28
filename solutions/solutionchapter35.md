### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-18 | Alfred Jiang | - |

### 方案名称
Xcode - 调试技巧集合

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
Debug \ Xcode \ Crash

### 需求场景
1. 调试常见问题收集

### 参考链接
（无）

### 详细内容

#####1. unrecognized selector sent to instance 问题快速定位的方法

在Debug菜单中选择 Breakpoints -> Create Symbolic Breakpoint，在Symbol中填写如下方法签名：-[NSObject(NSObject) doesNotRecognizeSelector:]，然后再运行，错误时断点会停在真正导致崩溃的地方。

#####2. Swift Delegate Error show "use of undeclared type in swift project"

在 Swift 中，Delegate 名称不能和函数命相同

### 效果图
（无）

### 备注
（无）
