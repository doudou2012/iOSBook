### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-04-08 | Alfred Jiang | - |
| 1 | 2015-08-18 | Alfred Jiang | 更新OC示例 |

### 方案名称
Auto Layout - 手动添加 Auto Layout 约束

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
Autolayout \ Constraint \ 自动布局 \ 页面布局

### 需求场景
1. 部分页面或控件的代码载入需要实现代码添加约束

### 参考链接
1. [CSDN - Auto Layout 进阶](http://blog.csdn.net/ysy441088327/article/details/12558097)

### 详细内容

1. 首先设置 View 的 translatesAutoresizingMaskIntoConstraints 的属性为 NO;
2. 然后将子视图 addSubview 到父视图中,否则在添加约束时会产生编译器警告;
3. 添加约束条件.


Objective-C 示例

        - (void)addView:(UIView *)aSubView toView:(UIView *)aPView
        {
            aSubView.translatesAutoresizingMaskIntoConstraints = NO;
            [aPView addSubview:aSubView];
            [aPView addConstraint:[NSLayoutConstraint constraintWithItem:aSubView attribute:NSLayoutAttributeTop relatedBy:NSLayoutRelationEqual toItem:aPView attribute:NSLayoutAttributeTop multiplier:1.0 constant:0]];
            [aPView addConstraint:[NSLayoutConstraint constraintWithItem:aSubView attribute:NSLayoutAttributeLeft relatedBy:NSLayoutRelationEqual toItem:aPView attribute:NSLayoutAttributeLeft multiplier:1.0 constant:0]];
            [aPView addConstraint:[NSLayoutConstraint constraintWithItem:aSubView attribute:NSLayoutAttributeRight relatedBy:NSLayoutRelationEqual toItem:aPView attribute:NSLayoutAttributeRight multiplier:1.0 constant:0]];
            [aPView addConstraint:[NSLayoutConstraint constraintWithItem:aSubView attribute:NSLayoutAttributeBottom relatedBy:NSLayoutRelationEqual toItem:aPView attribute:NSLayoutAttributeBottom multiplier:1.0 constant:0]];
            [aPView layoutSubviews];
        }


Swift 示例

            let bidButton = UIButton();

            bidButton.setTranslatesAutoresizingMaskIntoConstraints(false)

            self.viewBid.addSubview(bidButton);

            self.viewBid.addConstraints([
                NSLayoutConstraint(item: bidButton, attribute: NSLayoutAttribute.Top, relatedBy: NSLayoutRelation.Equal, toItem: self.viewBid, attribute: NSLayoutAttribute.Top, multiplier: 1.0, constant: 0),
                NSLayoutConstraint(item: bidButton, attribute: NSLayoutAttribute.Left, relatedBy: NSLayoutRelation.Equal, toItem: self.viewBid, attribute: NSLayoutAttribute.Left, multiplier: 1.0, constant: 0),
                NSLayoutConstraint(item: bidButton, attribute: NSLayoutAttribute.Right, relatedBy: NSLayoutRelation.Equal, toItem: self.viewBid, attribute: NSLayoutAttribute.Right, multiplier: 1.0, constant: 0),
                NSLayoutConstraint(item: bidButton, attribute: NSLayoutAttribute.Bottom, relatedBy: NSLayoutRelation.Equal, toItem: self.viewBid, attribute: NSLayoutAttribute.Bottom, multiplier: 1.0, constant: 0)
                ])

### 效果图
（无）

### 备注
