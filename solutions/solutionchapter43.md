### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-23 | Alfred Jiang | - |

### 方案名称
地图 - 为 Xcode 工程添加自定义初始化位置（载入 gpx 文件）

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
地图 \ 坐标 \ 初始位置 \ gpx

### 需求场景
1. 在模拟器中实现地图位置模拟
2. 在示例场景中实现位置模拟
3. 地图读取固定位置需求

### 参考链接
1. [XCode 4.2 地点模拟技巧](http://longtimenoc.com/archives/xcode-4-2-%E5%9C%B0%E7%82%B9%E6%A8%A1%E6%8B%9F%E6%8A%80%E5%B7%A7)

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
