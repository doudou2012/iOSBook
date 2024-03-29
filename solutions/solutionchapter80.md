### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-04-25 | Alfred Jiang | - |

### 方案名称
手势 - 实现手势操作介绍

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
Gesture Recognizer \ 手势操作

### 需求场景
1. 需要对页面增加手势操作响应时

### 参考链接
1. [iOS Developer Library](https://developer.apple.com/library/ios/navigation/)
2. [iOS手势识别的详细使用：拖动、缩放、旋转、点击、手势依赖、自定义手势](http://blog.jobbole.com/65846/)
3. [UIGestureRecognizer Tutorial in iOS 5: Pinches, Pans, and More!](http://www.raywenderlich.com/6567/uigesturerecognizer-tutorial-in-ios-5-pinches-pans-and-more)

### 详细内容

##### SDK 提供的手势

| 序号 | 手势 | Class | 说明 |
|:-------------: |:---------------:|:-------------:|:-------------:|
| 1 | Tap Gesture Recognizer | [UITapGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITapGestureRecognizer_Class/) | 点击手势 |
| 2 | Pinch Gesture Recognizer | [UIPinchGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIPinchGestureRecognizer_Class/) | 二指往內或往外拨动，平时经常用到的缩放 |
| 3 | Rotation Gesture Recognizer | [UIRotationGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIRotationGestureRecognizer_Class/) | 旋转手势 |
| 4 | Swipe Gesture Recognizer | [UISwipeGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISwipeGestureRecognizer_Class/) | 滑动，快速移动 |
| 5 | Pan Gesture Recognizer | [UIPanGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIPanGestureRecognizer_Class/) | 拖移，慢速移动 |
| 6 | Screen Edge Pan Gesture Recognizer | [UIScreenEdgePanGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIScreenEdgePanGestureRecognizer_Class/) | 屏幕边缘拖动手势 |
| 7 | Long Press Gesture Recognizer | [UILongPressGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILongPressGestureRecognizer_Class/) | 长按手势 |


##### 自定义手势举例

###### 定义 TickleGestureRecognizer

1. TickleGestureRecognizer.h
        #import <UIKit/UIKit.h>

        typedef enum {
            DirectionUnknown = 0,
            DirectionLeft,
            DirectionRight
        } Direction;

        @interface TickleGestureRecognizer : UIGestureRecognizer

        @property (assign) int tickleCount;
        @property (assign) CGPoint curTickleStart;
        @property (assign) Direction lastDirection;

        @end

2. TickleGestureRecognizer.m
        #import "TickleGestureRecognizer.h"
        #import <UIKit/UIGestureRecognizerSubclass.h>

        #define REQUIRED_TICKLES        2
        #define MOVE_AMT_PER_TICKLE     25

        @implementation TickleGestureRecognizer
        @synthesize tickleCount;
        @synthesize curTickleStart;
        @synthesize lastDirection;

        - (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
            UITouch * touch = [touches anyObject];
            self.curTickleStart = [touch locationInView:self.view];
        }

        - (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event {

            // Make sure we've moved a minimum amount since curTickleStart
            UITouch * touch = [touches anyObject];
            CGPoint ticklePoint = [touch locationInView:self.view];
            CGFloat moveAmt = ticklePoint.x - curTickleStart.x;
            Direction curDirection;
            if (moveAmt < 0) {
                curDirection = DirectionLeft;
            } else {
                curDirection = DirectionRight;
            }
            if (ABS(moveAmt) < MOVE_AMT_PER_TICKLE) return;

            // Make sure we've switched directions
            if (self.lastDirection == DirectionUnknown ||
                (self.lastDirection == DirectionLeft && curDirection == DirectionRight) ||
                (self.lastDirection == DirectionRight && curDirection == DirectionLeft)) {

                // w00t we've got a tickle!
                self.tickleCount++;
                self.curTickleStart = ticklePoint;
                self.lastDirection = curDirection;

                // Once we have the required number of tickles, switch the state to ended.
                // As a result of doing this, the callback will be called.
                if (self.state == UIGestureRecognizerStatePossible && self.tickleCount > REQUIRED_TICKLES) {
                    [self setState:UIGestureRecognizerStateEnded];
                }
            }

        }

        - (void)resetState {
            self.tickleCount = 0;
            self.curTickleStart = CGPointZero;
            self.lastDirection = DirectionUnknown;
            if (self.state == UIGestureRecognizerStatePossible) {
                [self setState:UIGestureRecognizerStateFailed];
            }
        }

        - (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event
        {
            [self resetState];
        }

        - (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event
        {
            [self resetState];
        }

        @end

###### 使用 TickleGestureRecognizer

1. ViewController.h
        // Add to top of file
        #import "TickleGestureRecognizer.h"

        // Add after @interface
        @property (strong) AVAudioPlayer * hehePlayer;
        - (void)handleTickle:(TickleGestureRecognizer *)recognizer;

2. ViewController.m
        // After @implementation
        @synthesize hehePlayer;

        // In viewDidLoad, right after TODO
        TickleGestureRecognizer * recognizer2 = [[TickleGestureRecognizer alloc] initWithTarget:self action:@selector(handleTickle:)];
        recognizer2.delegate = self;
        [view addGestureRecognizer:recognizer2];

        // At end of viewDidLoad
        self.hehePlayer = [self loadWav:@"hehehe1"];

        // Add at beginning of handlePan (gotta turn off pan to recognize tickles)
        return;

        // At end of file
        - (void)handleTickle:(TickleGestureRecognizer *)recognizer {
            [self.hehePlayer play];
        }

### 效果图
（无）

### 备注
