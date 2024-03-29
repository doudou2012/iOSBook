### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-27 | Alfred Jiang | - |

### 方案名称
UIScrollView - 给 UIScrollView 添加 Autolayout 约束条件

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UIScrollView \ Autolayout \ 自适应大小的 UIScrollView

### 需求场景
1. 一些应用的详情页面需要使用 UIScrollView 时

### 参考链接
1. [iOS: How To Make AutoLayout Work On A ScrollView](http://natashatherobot.com/ios-autolayout-scrollview/)

### 详细内容

#####1. 已纵向滑动为例

1. 首先，在需要滑动显示的页面 Xib 主 View 下拖放一个 UIScrollView ，并设置约束条件为上下左右四个方向距离主 View 都为 0；

2. 在 UIScrollView 中添加一个 UIView 作为 ContentView ，并设置约束条件为上下左右四个方向距离主 UIScrollView 都为 0；

3. 同时选中 UIScrollView 和 ContentView ，通过 Editor -> Pin -> Widths Equally 设置宽度相等；

4. 最重要的一步， ContentView 的高度需要根据其内容约束计算出来，ContentView 中距离 ContentView 最底部的一个 View 的约束条件至少包括 “底部距离 ContentView XX 像素” 且 自身高度可根据其他约束条件（与 ContentView 无关联的约束条件）计算出得出或直接设置得出。

#####2. 横向滑动设置步骤2的高度相等即可

### 效果图
（无）

### 备注
（无）
