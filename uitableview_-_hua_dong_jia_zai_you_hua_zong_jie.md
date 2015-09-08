### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-09-08 | Alfred Jiang | - |

### 方案名称
UITableView - 滑动加载性能优化总结

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UITableView \ UITableViewCell \ reloadData \ 列表 \ 滑动 \ 卡顿 \ 性能优化

### 需求场景
1. 实现较为复杂的 UITableViewCell 列表和加载大量数据时

### 参考链接
1. [CocoaChina - 一次 TableView 性能优化经历](http://www.cocoachina.com/ios/20150906/13212.html)
2. [伯乐在线 - iOS应用性能调优的25个建议和技巧](http://blog.jobbole.com/37984/)
3. 

### 详细内容

1. 列表卡顿问题最好真机测试，有条件的尽量选择低版本硬件和系统进行测试；
2. 使用 Instruments 的 Time Profiler 工具定位造成卡顿时间消耗的位置；
3. 避免 UITableView 的多次刷新，尤其 Xib 加载 UITableView 时避免首次自动加载；
3. 为 Cell 专门定义显示 Model；
4. Model 需要包含已提前计算出的 Cell 高度；
5. 对于显示的 NSString，提前在 Model 中组装完成，避免在 Cell 中组装转换；
6. 对于需要加载的网络图片链接，提前在 Model 中组装完成 NSURL,避免在 Cell 中组装转换；
7. 尽量减少 Cell 中的逻辑判断和运算;
8. 避免在 Cell 中反复创建 View,最好在初始化时一并创建，通过设置 Hidden 属性控制显示和隐藏;
9. 对于 UIImageView ,注意加载的图片大小是否与控件大小一致，尽量保持一致；
10. 正确使用 reuseIdentifier 
    
        static NSString *CellIdentifier = @"Cell";
        UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier forIndexPath:indexPath];

![image](/images/ReuseIdentifier.png)
### 效果图
（无）

### 备注
（无）