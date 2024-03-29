### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-12 | Alfred Jiang | - |

### 方案名称
二维码 - QRCode 生成与识别

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
QRCode \ 二维码 \ 扫描 \ 识别

### 需求场景
1. 需要使用到二维码的扫描与识别场景

### 参考链接
1. [Stackoverflow](http://stackoverflow.com/questions/7587681/how-to-use-c-code-libqrencode-in-an-ios-project)
2. [iOS自带扫描 和 生成二维码](http://blog.csdn.net/mdk132/article/details/42142971)
3. [在iOS和Android中使用二维码ZXing库及常见问题解决和整合后的代码](http://thierry-xing.iteye.com/blog/1815295)

### 详细内容
1. [ZBar](http://zbar.sourceforge.net/iphone/sdkdoc/install.html)
2. [ZXing](http://blog.devtang.com/blog/2012/12/23/use-zxing-library/)
3. [ibqrencode 生成二维码](http://fukuchi.org/works/qrencode/) 配合 [iOS 7 自带扫描QRCode](https://gist.github.com/Alex04/6976945)

AMScanViewController.h

    //
    //  AMScanViewController.h
    //
    //
    //  Created by Alexander Mack on 11.10.13.
    //  Copyright (c) 2013 ama-dev.com. All rights reserved.
    //

    #import <UIKit/UIKit.h>
    #import <AVFoundation/AVFoundation.h>

    @protocol AMScanViewControllerDelegate;

    @interface AMScanViewController : UIViewController <AVCaptureMetadataOutputObjectsDelegate>

    @property (nonatomic, weak) id<AMScanViewControllerDelegate> delegate;

    @property (assign, nonatomic) BOOL touchToFocusEnabled;

    - (BOOL) isCameraAvailable;
    - (void) startScanning;
    - (void) stopScanning;
    - (void) setTorch:(BOOL) aStatus;

    @end

    @protocol AMScanViewControllerDelegate <NSObject>

    @optional

    - (void) scanViewController:(AMScanViewController *) aCtler didTapToFocusOnPoint:(CGPoint) aPoint;
    - (void) scanViewController:(AMScanViewController *) aCtler didSuccessfullyScan:(NSString *) aScannedValue;

    @end

AMScanViewController.m

    //
    //  AMScanViewController.m
    //
    //
    //  Created by Alexander Mack on 11.10.13.
    //  Copyright (c) 2013 ama-dev.com. All rights reserved.
    //

    #import "AMScanViewController.h"

    @interface AMScanViewController ()

    @property (strong, nonatomic) AVCaptureDevice* device;
    @property (strong, nonatomic) AVCaptureDeviceInput* input;
    @property (strong, nonatomic) AVCaptureMetadataOutput* output;
    @property (strong, nonatomic) AVCaptureSession* session;
    @property (strong, nonatomic) AVCaptureVideoPreviewLayer* preview;

    @end

    @implementation AMScanViewController

    - (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil
    {
        self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
        if (self) {
        }
        return self;
    }

    - (void)viewWillAppear:(BOOL)animated;
    {
        [super viewWillAppear:animated];
        if(![self isCameraAvailable]) {
            [self setupNoCameraView];
        }
    }

    - (void) viewDidAppear:(BOOL)animated;
    {
        [super viewDidAppear:animated];
    }

    - (void)viewDidLoad
    {
        [super viewDidLoad];
        if([self isCameraAvailable]) {
            [self setupScanner];
        }
    }

    - (void)didReceiveMemoryWarning
    {
        [super didReceiveMemoryWarning];
        // Dispose of any resources that can be recreated.
    }

    - (void)touchesBegan:(NSSet*)touches withEvent:(UIEvent*)evt
    {
        if(self.touchToFocusEnabled) {
            UITouch *touch=[touches anyObject];
            CGPoint pt= [touch locationInView:self.view];
            [self focus:pt];
        }
    }

    #pragma mark -
    #pragma mark NoCamAvailable

    - (void) setupNoCameraView;
    {
        UILabel *labelNoCam = [[UILabel alloc] init];
        labelNoCam.text = @"No Camera available";
        labelNoCam.textColor = [UIColor blackColor];
        [self.view addSubview:labelNoCam];
        [labelNoCam sizeToFit];
        labelNoCam.center = self.view.center;
    }

    - (NSUInteger)supportedInterfaceOrientations;
    {
        return UIInterfaceOrientationMaskLandscape;
    }

    - (BOOL)shouldAutorotate;
    {
        return (UIDeviceOrientationIsLandscape([[UIDevice currentDevice] orientation]));
    }

    - (void)didRotateFromInterfaceOrientation:(UIInterfaceOrientation)fromInterfaceOrientation;
    {
        if([[UIDevice currentDevice] orientation] == UIDeviceOrientationLandscapeLeft) {
            AVCaptureConnection *con = self.preview.connection;
            con.videoOrientation = AVCaptureVideoOrientationLandscapeRight;
        } else {
            AVCaptureConnection *con = self.preview.connection;
            con.videoOrientation = AVCaptureVideoOrientationLandscapeLeft;
        }
    }

    #pragma mark -
    #pragma mark AVFoundationSetup

    - (void) setupScanner;
    {
        self.device = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];

        self.input = [AVCaptureDeviceInput deviceInputWithDevice:self.device error:nil];

        self.session = [[AVCaptureSession alloc] init];

        self.output = [[AVCaptureMetadataOutput alloc] init];
        [self.session addOutput:self.output];
        [self.session addInput:self.input];

        [self.output setMetadataObjectsDelegate:self queue:dispatch_get_main_queue()];
        self.output.metadataObjectTypes = @[AVMetadataObjectTypeQRCode];

        self.preview = [AVCaptureVideoPreviewLayer layerWithSession:self.session];
        self.preview.videoGravity = AVLayerVideoGravityResizeAspectFill;
        self.preview.frame = CGRectMake(0, 0, self.view.frame.size.width, self.view.frame.size.height);

        AVCaptureConnection *con = self.preview.connection;

        con.videoOrientation = AVCaptureVideoOrientationLandscapeLeft;

        [self.view.layer insertSublayer:self.preview atIndex:0];
    }

    #pragma mark -
    #pragma mark Helper Methods

    - (BOOL) isCameraAvailable;
    {
        NSArray *videoDevices = [AVCaptureDevice devicesWithMediaType:AVMediaTypeVideo];
        return [videoDevices count] > 0;
    }

    - (void)startScanning;
    {
        [self.session startRunning];

    }

    - (void) stopScanning;
    {
        [self.session stopRunning];
    }

    - (void) setTorch:(BOOL) aStatus;
    {
      	AVCaptureDevice *device = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
    		[device lockForConfiguration:nil];
    		if ( [device hasTorch] ) {
    			if ( aStatus ) {
    				[device setTorchMode:AVCaptureTorchModeOn];
    			} else {
    				[device setTorchMode:AVCaptureTorchModeOff];
    			}
    		}
    		[device unlockForConfiguration];
    }

    - (void) focus:(CGPoint) aPoint;
    {
        AVCaptureDevice *device = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
        if([device isFocusPointOfInterestSupported] &&
           [device isFocusModeSupported:AVCaptureFocusModeAutoFocus]) {
            CGRect screenRect = [[UIScreen mainScreen] bounds];
            double screenWidth = screenRect.size.width;
            double screenHeight = screenRect.size.height;
            double focus_x = aPoint.x/screenWidth;
            double focus_y = aPoint.y/screenHeight;
            if([device lockForConfiguration:nil]) {
                if([self.delegate respondsToSelector:@selector(scanViewController:didTapToFocusOnPoint:)]) {
                    [self.delegate scanViewController:self didTapToFocusOnPoint:aPoint];
                }
                [device setFocusPointOfInterest:CGPointMake(focus_x,focus_y)];
                [device setFocusMode:AVCaptureFocusModeAutoFocus];
                if ([device isExposureModeSupported:AVCaptureExposureModeAutoExpose]){
                    [device setExposureMode:AVCaptureExposureModeAutoExpose];
                }
                [device unlockForConfiguration];
            }
        }
    }

    #pragma mark -
    #pragma mark AVCaptureMetadataOutputObjectsDelegate

    - (void)captureOutput:(AVCaptureOutput *)captureOutput didOutputMetadataObjects:(NSArray *)metadataObjects
           fromConnection:(AVCaptureConnection *)connection
    {
        for(AVMetadataObject *current in metadataObjects) {
            if([current isKindOfClass:[AVMetadataMachineReadableCodeObject class]]) {
                if([self.delegate respondsToSelector:@selector(scanViewController:didSuccessfullyScan:)]) {
                    NSString *scannedValue = [((AVMetadataMachineReadableCodeObject *) current) stringValue];
                    [self.delegate scanViewController:self didSuccessfullyScan:scannedValue];
                }
            }
        }
    }

    @end


### 效果图
（无）

### 备注
（无）
