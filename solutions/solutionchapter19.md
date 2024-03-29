### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-03 | Alfred Jiang | - |

### 方案名称
动画 - 页面跳转 - 自定义模态跳转动画

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
跳转动画 \ UINavigationController \ UINavigationControllerDelegate \ UIViewControllerAnimatedTransitioning

### 需求场景
1. 对页面跳转动画有特殊需求时

### 参考链接
1. [How To Make A View Controller Transition Animation Like in the Ping App](http://www.raywenderlich.com/86521/how-to-make-a-view-controller-transition-animation-like-in-the-ping-app)

### 详细内容

#####UINavigationController
1. Swift 解决方案

定义

NavigationControllerDelegate.swift

    //
    //  NavigationControllerDelegate.swift
    //  CircleTransition
    //
    //  Created by Rounak Jain on 23/10/14.
    //  Copyright (c) 2014 Rounak Jain. All rights reserved.
    //

    import UIKit

    class NavigationControllerDelegate: NSObject, UINavigationControllerDelegate {

        var startPoint : CGPoint?
        weak var navigationController: UINavigationController?
        var interactionController: UIPercentDrivenInteractiveTransition?

        override func awakeFromNib() {
            super.awakeFromNib()
            var panGesture = UIPanGestureRecognizer(target: self, action: Selector("panned:"))
            self.navigationController!.view.addGestureRecognizer(panGesture)
        }

        func panned(gestureRecognizer: UIPanGestureRecognizer) {

            switch gestureRecognizer.state {
            case .Began:

                startPoint = gestureRecognizer.locationInView(self.navigationController?.view)

                if  startPoint!.x < 20
                {
                    self.interactionController = UIPercentDrivenInteractiveTransition()
                    if self.navigationController?.viewControllers.count > 1 {
                        GJViewTools.CLICK_POINT = gestureRecognizer.locationInView(self.navigationController?.view)
                        GJViewTools.DRAG_POINT = true
                        self.navigationController?.popViewControllerAnimated(true)
                    }
                }

            case .Changed:

                if  startPoint!.x < 20
                {
                    var translation = gestureRecognizer.translationInView(self.navigationController!.view)
                    var completionProgress = translation.x/CGRectGetWidth(self.navigationController!.view.bounds)
                    self.interactionController?.updateInteractiveTransition(completionProgress)
                }

            case .Ended:

                if  startPoint!.x < 20
                {

                    if (gestureRecognizer.velocityInView(self.navigationController!.view).x > 0) {
                        self.interactionController?.finishInteractiveTransition()
                    } else {
                        self.interactionController?.cancelInteractiveTransition()
                    }
                    self.interactionController = nil
                }

            default:
                self.interactionController?.cancelInteractiveTransition()
                self.interactionController = nil
            }
        }

        func navigationController(navigationController: UINavigationController, animationControllerForOperation operation: UINavigationControllerOperation, fromViewController fromVC: UIViewController, toViewController toVC: UIViewController) -> UIViewControllerAnimatedTransitioning? {
            var cirAnimator : CircleTransitionAnimator = CircleTransitionAnimator()
            cirAnimator.navigationOperation = operation
            return cirAnimator
        }

        func navigationController(navigationController: UINavigationController, interactionControllerForAnimationController animationController: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
            return self.interactionController
        }
    }

CircleTransitionAnimator.swift

    //
    //  CircleTransitionAnimator.swift
    //  CircleTransition
    //
    //  Created by Rounak Jain on 23/10/14.
    //  Copyright (c) 2014 Rounak Jain. All rights reserved.
    //

    import UIKit

    class CircleTransitionAnimator: NSObject, UIViewControllerAnimatedTransitioning {

        weak var transitionContext: UIViewControllerContextTransitioning?
        var navigationOperation: UINavigationControllerOperation?

        func transitionDuration(transitionContext: UIViewControllerContextTransitioning) -> NSTimeInterval {
            return GJViewTools.DURATION_TIME;
        }

        func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
            self.transitionContext = transitionContext

            var containerView = transitionContext.containerView()
            var fromViewController : UIViewController = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)!
            var toViewController : UIViewController = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)!
            var button = UIButton(frame: CGRectMake(0, 0, 10, 10))

            button.center = GJViewTools.CLICK_POINT

            if navigationOperation == UINavigationControllerOperation.Pop && !GJViewTools.DRAG_POINT {

                var snapshotImageView: UIView = fromViewController.view.snapshotViewAfterScreenUpdates(false)
                toViewController.view.addSubview(snapshotImageView)
                containerView.addSubview(toViewController.view)

                var circleMaskPathInitial = UIBezierPath(ovalInRect: button.frame)
                var radius = sqrt((SCREEN_HEIGH*SCREEN_HEIGH) + (SCREEN_WIDTH*SCREEN_WIDTH))
                var circleMaskPathFinal = UIBezierPath(ovalInRect: CGRectInset(button.frame, -radius, -radius))

                var maskLayer = CAShapeLayer()
                maskLayer.path = circleMaskPathFinal.CGPath
                snapshotImageView.layer.mask = maskLayer

                var maskLayerAnimation = CABasicAnimation(keyPath: "path")
                maskLayerAnimation.fromValue = circleMaskPathFinal.CGPath
                maskLayerAnimation.toValue = circleMaskPathInitial.CGPath
                maskLayerAnimation.duration = self.transitionDuration(transitionContext)
                maskLayerAnimation.delegate = self
                maskLayer.addAnimation(maskLayerAnimation, forKey: "path")

                self.delay(self.transitionDuration(transitionContext) - 0.1, closure: { () -> () in
                    snapshotImageView.removeFromSuperview()
                })
            }
            else
            {
                containerView.addSubview(toViewController.view)

                var circleMaskPathInitial = UIBezierPath(ovalInRect: button.frame)
                var radius = sqrt((SCREEN_HEIGH*SCREEN_HEIGH) + (SCREEN_WIDTH*SCREEN_WIDTH))
                var circleMaskPathFinal = UIBezierPath(ovalInRect: CGRectInset(button.frame, -radius, -radius))

                var maskLayer = CAShapeLayer()
                maskLayer.path = circleMaskPathFinal.CGPath
                toViewController.view.layer.mask = maskLayer

                var maskLayerAnimation = CABasicAnimation(keyPath: "path")
                maskLayerAnimation.fromValue = circleMaskPathInitial.CGPath
                maskLayerAnimation.toValue = circleMaskPathFinal.CGPath
                maskLayerAnimation.duration = self.transitionDuration(transitionContext)
                maskLayerAnimation.delegate = self
                maskLayer.addAnimation(maskLayerAnimation, forKey: "path")
            }
        }

        override func animationDidStop(anim: CAAnimation!, finished flag: Bool) {
            self.transitionContext?.completeTransition(!self.transitionContext!.transitionWasCancelled())
            self.transitionContext?.viewControllerForKey(UITransitionContextFromViewControllerKey)?.view.layer.mask = nil
        }

    }

使用

    var navDelegate : NavigationControllerDelegate = NavigationControllerDelegate()

    navDelegate.navigationController = frontNav
    frontNav.delegate = navDelegate
    navDelegate.awakeFromNib()

#####ModelTransition

1. Swift 解决方案

定义

ModelTransitionDelegate.swift

    //
    //  ModelTransitionDelegate.swift
    //  GrandJustice
    //
    //  Created by Alfred Jiang on 2/6/15.
    //  Copyright (c) 2015 FYH. All rights reserved.
    //

    import UIKit

    enum ModalPresentingType {
        case Present, Dismiss
    }

    class ModelTransitionDelegate: NSObject,UIViewControllerTransitioningDelegate {


        func animationControllerForPresentedController(presented: UIViewController, presentingController presenting: UIViewController, sourceController source: UIViewController) -> UIViewControllerAnimatedTransitioning? {

            var modelAnimator : ModelTransitionAnimator = ModelTransitionAnimator()
            modelAnimator.modalPresentingType = ModalPresentingType.Present

            return modelAnimator
        }

        func animationControllerForDismissedController(dismissed: UIViewController) -> UIViewControllerAnimatedTransitioning? {

            var modelAnimator : ModelTransitionAnimator = ModelTransitionAnimator()
            modelAnimator.modalPresentingType = ModalPresentingType.Dismiss

            return modelAnimator
        }

    }

ModelTransitionAnimator.swift

    //
    //  ModelTransitionAnimator.swift
    //  GrandJustice
    //
    //  Created by Alfred Jiang on 2/6/15.
    //  Copyright (c) 2015 FYH. All rights reserved.
    //

    import UIKit

    class ModelTransitionAnimator: NSObject,UIViewControllerAnimatedTransitioning {

        var modalPresentingType: ModalPresentingType?
        weak var transitionContext: UIViewControllerContextTransitioning?

        func transitionDuration(transitionContext: UIViewControllerContextTransitioning) -> NSTimeInterval {
            return 0.6
        }

        func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
            self.transitionContext = transitionContext

            var containerView = transitionContext.containerView()
            var fromViewController : UIViewController = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)!
            var toViewController : UIViewController = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)!
            var button = UIButton(frame: CGRectMake(0, 0, 10, 10))

            button.center = GJViewTools.CLICK_POINT

            if modalPresentingType == ModalPresentingType.Dismiss {

                var snapshotImageView: UIView = fromViewController.view.snapshotViewAfterScreenUpdates(false)
                toViewController.view.addSubview(snapshotImageView)
                containerView.addSubview(toViewController.view)

                var circleMaskPathInitial = UIBezierPath(ovalInRect: button.frame)
                var radius = sqrt((SCREEN_HEIGH*SCREEN_HEIGH) + (SCREEN_WIDTH*SCREEN_WIDTH))
                var circleMaskPathFinal = UIBezierPath(ovalInRect: CGRectInset(button.frame, -radius, -radius))

                var maskLayer = CAShapeLayer()
                maskLayer.path = circleMaskPathFinal.CGPath
                snapshotImageView.layer.mask = maskLayer

                var maskLayerAnimation = CABasicAnimation(keyPath: "path")
                maskLayerAnimation.fromValue = circleMaskPathFinal.CGPath
                maskLayerAnimation.toValue = circleMaskPathInitial.CGPath
                maskLayerAnimation.duration = self.transitionDuration(transitionContext)
                maskLayerAnimation.delegate = self
                maskLayer.addAnimation(maskLayerAnimation, forKey: "path")

                self.delay(self.transitionDuration(transitionContext) - 0.1, closure: { () -> () in
                    snapshotImageView.removeFromSuperview()
                })
            }
            else
            {
                containerView.addSubview(toViewController.view)

                var circleMaskPathInitial = UIBezierPath(ovalInRect: button.frame)
                var radius = sqrt((SCREEN_HEIGH*SCREEN_HEIGH) + (SCREEN_WIDTH*SCREEN_WIDTH))
                var circleMaskPathFinal = UIBezierPath(ovalInRect: CGRectInset(button.frame, -radius, -radius))

                var maskLayer = CAShapeLayer()
                maskLayer.path = circleMaskPathFinal.CGPath
                toViewController.view.layer.mask = maskLayer

                var maskLayerAnimation = CABasicAnimation(keyPath: "path")
                maskLayerAnimation.fromValue = circleMaskPathInitial.CGPath
                maskLayerAnimation.toValue = circleMaskPathFinal.CGPath
                maskLayerAnimation.duration = self.transitionDuration(transitionContext)
                maskLayerAnimation.delegate = self
                maskLayer.addAnimation(maskLayerAnimation, forKey: "path")
            }
        }

        override func animationDidStop(anim: CAAnimation!, finished flag: Bool) {
            self.transitionContext?.completeTransition(!self.transitionContext!.transitionWasCancelled())
            self.transitionContext?.viewControllerForKey(UITransitionContextFromViewControllerKey)?.view.layer.mask = nil
        }
    }


使用

    var transDelegate : ModelTransitionDelegate = ModelTransitionDelegate()

    var helpNav = UINavigationController(rootViewController: tipsVC)
    helpNav.transitioningDelegate = self.transDelegate

    self.presentViewController(helpNav, animated: true, completion: { () -> Void in

    })




### 效果图
（无）

### 备注
（无）
