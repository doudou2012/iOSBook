### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-05-07 | Alfred Jiang | - |

### 方案名称
应用间通信 - 通过 URL 检测是否安装并打开应用

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
URL \ Web \ 邮件打开App \ Schema

### 需求场景
1. 需要通过 URL 检测是否安装应用，如果已安装则打开应用，如果未安装则跳转到下载页面

### 参考链接
1. [iOS使用schema协议调起APP](http://mweb.baidu.com/p/ios%E4%BD%BF%E7%94%A8schema%E5%8D%8F%E8%AE%AE%E8%B0%83%E8%B5%B7app.html)
2. [在mobile safari中巧妙实现检测应用安装就打开，否则进App Store下载](http://www.iunbug.com/archives/2012/09/18/401.html)
3. [IOS在一个程序中启动另一个程序](http://blog.csdn.net/zzzili/article/details/8449893)

### 详细内容

##### 1. App 设置
在 .plist 文件添加如下字段

![schematest](images/schematest.png)

    <key>CFBundleURLTypes</key>
    <array>
		<dict>
			<key>CFBundleURLName</key>
			<string></string>
			<key>CFBundleURLSchemes</key>
			<array>
				<string>GJApplication</string>
			</array>
		</dict>
	</array>

* GJApplication 替换为自定义名称

##### 2. Web 代码

1. 示例一

```
    <body>
    <div>
    Click to open GJ App
    <br />
    <a href="REXApplication://com.acme.ToDoList"></a>
    <a onClick="javascript:try_to_open_app();" href="REXApplication://com.acme.ToDoList">Open GJ App</a>
    </div>
    <script language="javascript">
    var timeout;
    function open_appstore() {
    window.location='http://itunes.apple.com/cn/app/id950554426?mt=8';
    }

    function try_to_open_app() {
    timeout = setTimeout('open_appstore()', 300);
    }
    </script>
    </body>
```

2. 示例二

```
 <!DOCTYPE html>
<html>
    <body>
        <script type="text/javascript">
            window.onload = function() {
                // Deep link to your app goes here
                document.getElementById("l").src = "REXApplication://";

                setTimeout(function() {
                    // Link to the App Store should go here -- only fires if deep link fails
                    window.location = "http://www.pgyer.com/irex";
                }, 300);
            };
        </script>
        <iframe id="l" width="1" height="1" style="visibility:hidden"></iframe>
    </body>
</html>
```


* REXApplication://com.acme.ToDoList 中 com.acme.ToDoList 可设置为自定义参数
* http://itunes.apple.com/cn/app/id950554426?mt=8 替换为实际安装地址（如果是 AppStore 安装，替换 id950554426 即可）

##### 3. 测试链接
1. [测试链接一](120.27.34.52/opengj.html)
2. [测试链接二](120.27.34.52/startREX.html)

### 效果图
（无）

### 备注
（无）
