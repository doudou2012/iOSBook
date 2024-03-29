### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-26 | Alfred Jiang | - |

### 方案名称
键盘 - 使用 IQKeyboardManager 完美解决IOS开发键盘遮挡

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
键盘 \ Keyboard \ 弹出键盘 \ 收起键盘

### 需求场景
1. 需要弹出键盘收起键盘的页面避免遮挡问题

### 参考链接
1. [IQKeyboardManager-不用写一行代码就完美解决IOS开发键盘遮挡的类库](http://www.livyfeel.com/iqkeyboardmanager/)
2. [GitHub : IQKeyboardManager](https://github.com/hackiftekhar/IQKeyboardManager)

### 详细内容

#####1. Cocoapod安装:

你可以使用 cocoapod 来安装 IQKeyboardManager 类库。在Podfile文件中这样写：

    pod 'IQKeyboardManager'

就可以了

#####2. Framework加入:

将KeyboardManager.framework 、 IQKeyboardManager.bundle等文件加入到项目中即可。详细可以[下载Demo](https://github.com/hackiftekhar/IQKeyboardManager)并查看。

注意：需要在项目的设置other linker flag中加入-ObjC。

建议：目前Cocoapods已经是很成熟的第三方类库管理工具了，推荐使用。

#####3. 常用的属性和方法介绍

1. +sharedManager：获取类库的单例变量。我们也知道，一个项目中都是使用一个类库的单例的，不然每一个输入框我们怎么好控制呢？所以如果你想自己修改一下界面，那么就要先获取到这个单例的变量，然后在往下面操作。

2. enable：这个属性就是说，我们的项目里面使用不适用这个类库所提供的输入框不遮挡技术。如果您再某些页面里面不需要，可以在获取到单例之后，将这个enable变量设置为FALSE。

3. keyboardDistanceFromTextField：这个就是我们的输入框距离我们的键盘的距离了。默认是10px。就是说输入框默认会自动移动到键盘的上面10个像素以方便用户输入。如果你需要自定义，可以改变这个值。

4. enableAutoToolbar：IQKeyboardManager提供的键盘上面默认会有“前一个”“后一个”“完成”这样的辅助按钮。如果你不需要，可以将这个enableAutoToolbar属性设置为NO，这样就不会显示了。

5. toolbarManageBehaviour：如果有多个输入框，那么我们在输入的时候可以通过点击在Toolbar中的“前一个”“后一个”按钮来实现移动到不同的输入框。可是输入框的移动肯定是有一个规律的。这里就提供了两个方式。第一种就是加入的顺序，第二种就是按照Tag值的大小排序。这个属性可以设置两个参数：IQAutoToolbarBySubviews 和IQAutoToolbarByTag 。

6. shouldToolbarUsesTextFieldTintColor：这个是用来将输入框的tinicolor和toolbar的tinicolor相互协调的，默认为NO。

7. shouldShowTextFieldPlaceholder：如果输入框友placeholder的话，那么在toolbar中默认会显示出来。在中间的部分会显示uitextfield的placeholder。如果你不需要，可以设置NO。

8. placeholderFont ：这个就是toolbar中显示placeholder的字体大小了。你可以自定义通过传入一个font。

9. canAdjustTextView ：这样说，如果你的输入框有600px的高度。那么在点击输入框的时候，键盘弹出来了，输入框会如何显示呢？如果把这个参数打开，那么输入框的高度会刚好的降低，就是说，你可以看到输入框的四个board，操作一下就会一目了然：）

### 效果图
（无）

### 备注
（无）

### 其他可选方案

* [键盘弹出与收起改变页面高度](solutions/solutionchapter15.md)
