### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-23 | Alfred Jiang | - |

### 方案名称
UITableView - 使用 EGORefreshTableHeaderView 实现下拉刷新 UITableView

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UITableView \ EGORefreshTableHeaderView \ 下拉刷新

### 需求场景
1. 需要实现列表数据的下拉刷新

### 参考链接
1. [IOS详解TableView——内置刷新，EGO，以及搜索显示控制器](http://blog.csdn.net/cocoarannie/article/details/11750519)
2. [GitHub](https://github.com/wanyakun/EGOTableViewPullRefresh)

### 详细内容

#####1. EGORefreshTableHeaderView(.h.m)

1. EGORefreshTableHeaderView.h

        //
        //  EGORefreshTableHeaderView.h
        //  Demo
        //
        //  Created by Devin Doty on 10/14/09October14.
        //  Copyright 2009 enormego. All rights reserved.
        //
        //  Permission is hereby granted, free of charge, to any person obtaining a copy
        //  of this software and associated documentation files (the "Software"), to deal
        //  in the Software without restriction, including without limitation the rights
        //  to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
        //  copies of the Software, and to permit persons to whom the Software is
        //  furnished to do so, subject to the following conditions:
        //
        //  The above copyright notice and this permission notice shall be included in
        //  all copies or substantial portions of the Software.
        //
        //  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
        //  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
        //  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
        //  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
        //  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
        //  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
        //  THE SOFTWARE.
        //

        #import <UIKit/UIKit.h>
        #import <QuartzCore/QuartzCore.h>

        typedef enum{
        	EGOOPullPulling = 0,
        	EGOOPullNormal,
        	EGOOPullLoading,
        } EGOPullState;

        #define DEFAULT_ARROW_IMAGE         [UIImage imageNamed:@"blueArrow.png"]
        #define DEFAULT_BACKGROUND_COLOR    [UIColor colorWithRed:226.0/255.0 green:231.0/255.0 blue:237.0/255.0 alpha:1.0]
        #define DEFAULT_TEXT_COLOR          [UIColor colorWithRed:87.0/255.0 green:108.0/255.0 blue:137.0/255.0 alpha:1.0]
        #define DEFAULT_ACTIVITY_INDICATOR_STYLE    UIActivityIndicatorViewStyleWhite

        #define FLIP_ANIMATION_DURATION 0.18f

        #define PULL_AREA_HEIGTH 60.0f
        #define PULL_TRIGGER_HEIGHT (PULL_AREA_HEIGTH + 5.0f)

        @protocol EGORefreshTableHeaderDelegate;
        @interface EGORefreshTableHeaderView : UIView {

        	id _delegate;
        	EGOPullState _state;

        	UILabel *_lastUpdatedLabel;
        	UILabel *_statusLabel;
        	CALayer *_arrowImage;
        	UIActivityIndicatorView *_activityView;

            // Set this to Yes when egoRefreshTableHeaderDidTriggerRefresh delegate is called and No with egoRefreshScrollViewDataSourceDidFinishedLoading
            BOOL isLoading;

        }

        @property(nonatomic,assign) id <EGORefreshTableHeaderDelegate> delegate;

        - (void)refreshLastUpdatedDate;
        - (void)egoRefreshScrollViewDidScroll:(UIScrollView *)scrollView;
        - (void)egoRefreshScrollViewDidEndDragging:(UIScrollView *)scrollView;
        - (void)egoRefreshScrollViewWillBeginDragging:(UIScrollView *)scrollView;
        - (void)egoRefreshScrollViewDataSourceDidFinishedLoading:(UIScrollView *)scrollView;
        - (void)startAnimatingWithScrollView:(UIScrollView *) scrollView;
        - (void)setBackgroundColor:(UIColor *)backgroundColor textColor:(UIColor *) textColor arrowImage:(UIImage *) arrowImage;

        @end

        @protocol EGORefreshTableHeaderDelegate
        - (void)egoRefreshTableHeaderDidTriggerRefresh:(EGORefreshTableHeaderView*)view;
        @optional
        - (NSDate*)egoRefreshTableHeaderDataSourceLastUpdated:(EGORefreshTableHeaderView*)view;

        @end

1. EGORefreshTableHeaderView.h

        //
        //  EGORefreshTableHeaderView.m
        //  Demo
        //
        //  Created by Devin Doty on 10/14/09October14.
        //  Copyright 2009 enormego. All rights reserved.
        //
        //  Permission is hereby granted, free of charge, to any person obtaining a copy
        //  of this software and associated documentation files (the "Software"), to deal
        //  in the Software without restriction, including without limitation the rights
        //  to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
        //  copies of the Software, and to permit persons to whom the Software is
        //  furnished to do so, subject to the following conditions:
        //
        //  The above copyright notice and this permission notice shall be included in
        //  all copies or substantial portions of the Software.
        //
        //  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
        //  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
        //  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
        //  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
        //  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
        //  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
        //  THE SOFTWARE.
        //

        #import "EGORefreshTableHeaderView.h"

        @interface EGORefreshTableHeaderView (Private)
        - (void)setState:(EGOPullState)aState;
        @end

        @implementation EGORefreshTableHeaderView

        @synthesize delegate=_delegate;

        - (id)initWithFrame:(CGRect)frame {
            if (self = [super initWithFrame:frame]) {

                isLoading = NO;

                CGFloat midY = frame.size.height - PULL_AREA_HEIGTH/2;

                /* Config Last Updated Label */
        		UILabel *label = [[UILabel alloc] initWithFrame:CGRectMake(0.0f, midY, self.frame.size.width, 20.0f)];
        		label.autoresizingMask = UIViewAutoresizingFlexibleWidth;
        		label.font = [UIFont systemFontOfSize:12.0f];
        		label.shadowOffset = CGSizeMake(0.0f, 1.0f);
        		label.backgroundColor = [UIColor clearColor];
        		label.textAlignment = NSTextAlignmentCenter;
        		[self addSubview:label];
        		_lastUpdatedLabel=label;

                /* Config Status Updated Label */
        		label = [[UILabel alloc] initWithFrame:CGRectMake(0.0f, midY - 18, self.frame.size.width, 20.0f)];
        		label.autoresizingMask = UIViewAutoresizingFlexibleWidth;
        		label.font = [UIFont boldSystemFontOfSize:13.0f];
        		label.shadowOffset = CGSizeMake(0.0f, 1.0f);
        		label.backgroundColor = [UIColor clearColor];
        		label.textAlignment = NSTextAlignmentCenter;
        		[self addSubview:label];
        		_statusLabel=label;

                /* Config Arrow Image */
        		CALayer *layer = [[CALayer alloc] init];
        		layer.frame = CGRectMake(25.0f,midY - 35, 30.0f, 55.0f);
        		layer.contentsGravity = kCAGravityResizeAspect;
        #if __IPHONE_OS_VERSION_MAX_ALLOWED >= 40000
        		if ([[UIScreen mainScreen] respondsToSelector:@selector(scale)]) {
        			layer.contentsScale = [[UIScreen mainScreen] scale];
        		}
        #endif
        		[[self layer] addSublayer:layer];
        		_arrowImage=layer;

                /* Config activity indicator */
        		UIActivityIndicatorView *view = [[UIActivityIndicatorView alloc] initWithActivityIndicatorStyle:DEFAULT_ACTIVITY_INDICATOR_STYLE];
        		view.frame = CGRectMake(25.0f,midY - 8, 20.0f, 20.0f);
        		[self addSubview:view];
        		_activityView = view;

        		[self setState:EGOOPullNormal];

                /* Configure the default colors and arrow image */
                [self setBackgroundColor:nil textColor:nil arrowImage:nil];

            }

            return self;

        }

        #pragma mark -
        #pragma mark Setters

        #define aMinute 60
        #define anHour 3600
        #define aDay 86400

        - (void)refreshLastUpdatedDate {
            NSDate * date = nil;
        	if ([_delegate respondsToSelector:@selector(egoRefreshTableHeaderDataSourceLastUpdated:)]) {
        		date = [_delegate egoRefreshTableHeaderDataSourceLastUpdated:self];
        	}
            if(date) {
                NSTimeInterval timeSinceLastUpdate = [date timeIntervalSinceNow];
                NSInteger timeToDisplay = 0;
                timeSinceLastUpdate *= -1;

                if(timeSinceLastUpdate < anHour) {
                    timeToDisplay = (NSInteger) (timeSinceLastUpdate / aMinute);

                    if(timeToDisplay == /* Singular*/ 1) {
                    _lastUpdatedLabel.text = [NSString stringWithFormat:NSLocalizedStringFromTable(@"更新于 %ld 分钟之前",@"PullTableViewLan",@"Last uppdate in minutes singular"),(long)timeToDisplay];
                    } else {
                        /* Plural */
                        _lastUpdatedLabel.text = [NSString stringWithFormat:NSLocalizedStringFromTable(@"更新于 %ld 分钟之前",@"PullTableViewLan",@"Last uppdate in minutes plural"), (long)timeToDisplay];

                    }

                } else if (timeSinceLastUpdate < aDay) {
                    timeToDisplay = (NSInteger) (timeSinceLastUpdate / anHour);
                    if(timeToDisplay == /* Singular*/ 1) {
                        _lastUpdatedLabel.text = [NSString stringWithFormat:NSLocalizedStringFromTable(@"更新于 %ld 小时之前",@"PullTableViewLan",@"Last uppdate in hours singular"), (long)timeToDisplay];
                    } else {
                        /* Plural */
                        _lastUpdatedLabel.text = [NSString stringWithFormat:NSLocalizedStringFromTable(@"更新于 %ld 小时之前",@"PullTableViewLan",@"Last uppdate in hours plural"), (long)timeToDisplay];

                    }

                } else {
                    timeToDisplay = (NSInteger) (timeSinceLastUpdate / aDay);
                    if(timeToDisplay == /* Singular*/ 1) {
                        _lastUpdatedLabel.text = [NSString stringWithFormat:NSLocalizedStringFromTable(@"更新于 %ld 天之前",@"PullTableViewLan",@"Last uppdate in days singular"), (long)timeToDisplay];
                    } else {
                        /* Plural */
                        _lastUpdatedLabel.text = [NSString stringWithFormat:NSLocalizedStringFromTable(@"更新于 %ld 天之前",@"PullTableViewLan",@"Last uppdate in days plural"), (long)timeToDisplay];
                    }

                }

            } else {
                _lastUpdatedLabel.text = nil;
            }

            // Center the status label if the lastupdate is not available
            CGFloat midY = self.frame.size.height - PULL_AREA_HEIGTH/2;
            if(!_lastUpdatedLabel.text) {
                _statusLabel.frame = CGRectMake(0.0f, midY - 8, self.frame.size.width, 20.0f);
            } else {
                _statusLabel.frame = CGRectMake(0.0f, midY - 18, self.frame.size.width, 20.0f);
            }

        }

        - (void)setState:(EGOPullState)aState{

        	switch (aState) {
        		case EGOOPullPulling:

        			_statusLabel.text = NSLocalizedStringFromTable(@"Release to refresh ...",@"PullTableViewLan", @"Release to refresh status");
        			[CATransaction begin];
        			[CATransaction setAnimationDuration:FLIP_ANIMATION_DURATION];
        			_arrowImage.transform = CATransform3DMakeRotation((M_PI / 180.0) * 180.0f, 0.0f, 0.0f, 1.0f);
        			[CATransaction commit];

        			break;
        		case EGOOPullNormal:

        			if (_state == EGOOPullPulling) {
        				[CATransaction begin];
        				[CATransaction setAnimationDuration:FLIP_ANIMATION_DURATION];
        				_arrowImage.transform = CATransform3DIdentity;
        				[CATransaction commit];
        			}

        			_statusLabel.text = NSLocalizedStringFromTable(@"Pull down to refresh ...",@"PullTableViewLan", @"Pull down to refresh status");
        			[_activityView stopAnimating];
        			[CATransaction begin];
        			[CATransaction setValue:(id)kCFBooleanTrue forKey:kCATransactionDisableActions];
        			_arrowImage.hidden = NO;
        			_arrowImage.transform = CATransform3DIdentity;
        			[CATransaction commit];

        			[self refreshLastUpdatedDate];

        			break;
        		case EGOOPullLoading:

        			_statusLabel.text = NSLocalizedStringFromTable(@"Loading ...",@"PullTableViewLan", @"Loading Status");
        			[_activityView startAnimating];
        			[CATransaction begin];
        			[CATransaction setValue:(id)kCFBooleanTrue forKey:kCATransactionDisableActions];
        			_arrowImage.hidden = YES;
        			[CATransaction commit];

        			break;
        		default:
        			break;
        	}

        	_state = aState;
        }

        - (void)setBackgroundColor:(UIColor *)backgroundColor textColor:(UIColor *) textColor arrowImage:(UIImage *) arrowImage
        {
            self.backgroundColor = backgroundColor? backgroundColor : DEFAULT_BACKGROUND_COLOR;

            if(textColor) {
                _lastUpdatedLabel.textColor = textColor;
                _statusLabel.textColor = textColor;
            } else {
                _lastUpdatedLabel.textColor = DEFAULT_TEXT_COLOR;
                _statusLabel.textColor = DEFAULT_TEXT_COLOR;
            }
            _lastUpdatedLabel.shadowColor = [_lastUpdatedLabel.textColor colorWithAlphaComponent:0.1f];
            _statusLabel.shadowColor = [_statusLabel.textColor colorWithAlphaComponent:0.1f];

            _arrowImage.contents = (id)(arrowImage? arrowImage.CGImage : DEFAULT_ARROW_IMAGE.CGImage);
        }

        #pragma mark -
        #pragma mark ScrollView Methods

        - (void)egoRefreshScrollViewDidScroll:(UIScrollView *)scrollView {

        	if (_state == EGOOPullLoading) {
        		CGFloat offset = MAX(scrollView.contentOffset.y * -1, 0);
        		offset = MIN(offset, PULL_AREA_HEIGTH);
                UIEdgeInsets currentInsets = scrollView.contentInset;
                currentInsets.top = offset;
                scrollView.contentInset = currentInsets;

        	} else if (scrollView.isDragging) {
        		if (_state == EGOOPullPulling && scrollView.contentOffset.y > -PULL_TRIGGER_HEIGHT && scrollView.contentOffset.y < 0.0f && !isLoading) {
        			[self setState:EGOOPullNormal];
        		} else if (_state == EGOOPullNormal && scrollView.contentOffset.y < -PULL_TRIGGER_HEIGHT && !isLoading) {
        			[self setState:EGOOPullPulling];

        		}

        		if (scrollView.contentInset.top != 0) {
                    UIEdgeInsets currentInsets = scrollView.contentInset;
                    currentInsets.top = 0;
                    scrollView.contentInset = currentInsets;
        		}

        	}

        }

        - (void)startAnimatingWithScrollView:(UIScrollView *) scrollView {
            isLoading = YES;

            [self setState:EGOOPullLoading];
            [UIView beginAnimations:nil context:NULL];
            [UIView setAnimationDuration:0.2];
            UIEdgeInsets currentInsets = scrollView.contentInset;
            currentInsets.top = PULL_AREA_HEIGTH;
            scrollView.contentInset = currentInsets;
            [UIView commitAnimations];
            if(scrollView.contentOffset.y == 0){
                [scrollView setContentOffset:CGPointMake(scrollView.contentOffset.x, -PULL_TRIGGER_HEIGHT) animated:YES];
            }
        }

        - (void)egoRefreshScrollViewDidEndDragging:(UIScrollView *)scrollView {

        	if (scrollView.contentOffset.y <= - PULL_TRIGGER_HEIGHT && !isLoading) {
                if ([_delegate respondsToSelector:@selector(egoRefreshTableHeaderDidTriggerRefresh:)]) {
                    [_delegate egoRefreshTableHeaderDidTriggerRefresh:self];
                }
                [self startAnimatingWithScrollView:scrollView];
        	}

        }

        - (void)egoRefreshScrollViewDataSourceDidFinishedLoading:(UIScrollView *)scrollView {

            isLoading = NO;

        	[UIView beginAnimations:nil context:NULL];
        	[UIView setAnimationDuration:.3];
            UIEdgeInsets currentInsets = scrollView.contentInset;
            currentInsets.top = 0;
            scrollView.contentInset = currentInsets;
        	[UIView commitAnimations];

        	[self setState:EGOOPullNormal];

        }

        - (void)egoRefreshScrollViewWillBeginDragging:(UIScrollView *)scrollView
        {
            [self refreshLastUpdatedDate];
        }

        #pragma mark -
        #pragma mark Dealloc

        - (void)dealloc {

        	_delegate=nil;
        	[_activityView release];
        	[_statusLabel release];
        	[_arrowImage release];
        	[_lastUpdatedLabel release];
            [super dealloc];
        }

        @end


#####2. 设置 ARC

    Project 名称 -> TARGETS -> Build Phases -> Compile Soucrce
    设置 EGORefreshTableHeaderView.m 属性 -fno-objc-arc

#####3. 引入并使用

1. 引入头文件
        #import "EGORefreshTableHeaderView.h"

2. 实例化 EGORefreshTableHeaderView 对象
        var headerRefreshView : EGORefreshTableHeaderView!

        func setupRefreshTableView()
        {
            headerRefreshView = EGORefreshTableHeaderView(frame: CGRectMake(0, 0 - CGRectGetHeight(self.tableViewMain.frame), CGRectGetWidth(self.tableViewMain.frame), CGRectGetHeight(self.tableViewMain.frame)))
            headerRefreshView.delegate = self
            self.tableViewMain.addSubview(headerRefreshView)
        }

3. 实现必要的代理
        EGORefreshTableHeaderDelegate 和 UIScrollViewDelegate

        // MARK : - Drag Refresh

        func egoRefreshTableHeaderDidTriggerRefresh(view: EGORefreshTableHeaderView!) {
            //
            println("egoRefreshTableHeaderDidTriggerRefresh")

            self.reloadTableViewDataSource()

            dispatch_after(
                dispatch_time(
                    DISPATCH_TIME_NOW,
                    Int64(3.0 * Double(NSEC_PER_SEC))
                ),
                dispatch_get_main_queue(),{
                  self.endReloadTableViewDataSource()
                })

        }

        func scrollViewDidScroll(scrollView: UIScrollView) {
            headerRefreshView.egoRefreshScrollViewDidScroll(scrollView)
        }

        func scrollViewDidEndDragging(scrollView: UIScrollView, willDecelerate decelerate: Bool)     {
            headerRefreshView.egoRefreshScrollViewDidEndDragging(scrollView)
        }

4. 实现刷新请求和结束操作函数
        func reloadTableViewDataSource()
        {

        }

        func endReloadTableViewDataSource()
        {
            self.headerRefreshView.egoRefreshScrollViewDataSourceDidFinishedLoading(self.tableViewMain)
        }

### 效果图
（无）

### 备注
（无）
