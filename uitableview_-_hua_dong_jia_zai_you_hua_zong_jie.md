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
1. 实现较为复杂的 UITableViewCell 列表或加载较多数据时

### 参考链接
1. [CocoaChina - 一次 TableView 性能优化经历](http://www.cocoachina.com/ios/20150906/13212.html)
2. []()

### 详细内容
#####1. 创建 gpx 文件，可命名为“TestLocation.gpx”

    New -> File -> iOS -> Resource -> GPX File

#####2. 添加坐标
    <?xml version="1.0"?>

    <gpx version="1.1" creator="Xcode">

        <wpt lat="37.331705" lon="-122.030237">

             <name>Cupertino</name>

        </wpt>

    </gpx>

#####3. 启动模拟器，可点击下方位置出现 “TestLocation” 选项

### 效果图
（无）

### 备注
（无）