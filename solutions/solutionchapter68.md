### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-04-03 | Alfred Jiang | - |

### 方案名称
动画 - 使用 POViewFrameBuilder 快速实现 UIView 的动画移动和布局

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UIView \ Layout \ Animation

### 需求场景
1. 动画实现子 UIView 的布局动画和动画移动效果

### 参考链接
1. [POViewFrameBuilder](https://github.com/podio/ios-view-frame-builder)

### 详细内容

1. 将 POViewFrameBuilder 文件加入工程
2. 引入 UIView+POViewFrameBuilder.h 头文件
3. 使用示例
        //Resizing a view:
        [view.po_frameBuilder setWidth:100.0f height:40.0f];

        //Moving a view to be centered within it's superview:
        [view.po_frameBuilder centerInSuperview];

        //You can combine these methods to your own liking:
        [[view.po_frameBuilder setWidth:100.0f height:40.0f] centerHorizontallyInSuperview];

### 效果图
（无）

### 备注
