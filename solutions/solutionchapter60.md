### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-30 | Alfred Jiang | - |

### 方案名称
UIPageControl - 翻页显示的实现

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UIPageControl \ 翻页 \ UIScrollView

### 需求场景
1. 需要实现 UIScrollView 翻页显示并显示页码标签时

### 参考链接
（无）

### 详细内容

#####1. Swift 版本

    func scrollViewDidScroll(scrollView: UIScrollView) {
        self.resetPageControl()
    }

    func resetPageControl()
    {
        var iPages : NSInteger = NSInteger(self.scrollViewAttachments.contentSize.width / self.scrollViewAttachments.frame.size.width)
        var iPage : NSInteger = NSInteger(self.scrollViewAttachments.contentOffset.x / self.scrollViewAttachments.frame.size.width)
        self.pageControlItem.numberOfPages = iPages
        self.pageControlItem.currentPage = iPage
    }

#####2. Objective-C 版本
    - (void)scrollViewDidScroll:(UIScrollView *)scrollView
    {
        [self resetPageControl];
    }

    -(void)resetPageControl
    {
        NSInteger iPages = self.scrollViewMain.contentSize.width / self.scrollViewMain.frame.size.width;
        NSInteger iPage = self.scrollViewMain.contentOffset.x / self.scrollViewMain.frame.size.width;
        self.pageControlItem.numberOfPages = iPages;
        self.pageControlItem.currentPage = iPage;
    }

#####3. UIScrollView 添加页面显示元素实现示例

    func setUpAttachments(attachmens : NSArray)
    {
        let pages = CGFloat(ceil(Double(CGFloat(attachmens.count) / 4.0))) //向上取整计算页总数
        self.scrollViewAttachments.contentSize = CGSizeMake(CGFloat((CGFloat(WIDTHSCREEN - 10.0)) * pages), 100.0)

        for obj in attachmens
        {
            var aAttachment : AuctionAttachment = obj as AuctionAttachment
            var aItemView : REXAttachmentView = REXAttachmentView.loadFromNibNamed("REXAttachmentView", bundle: nil) as REXAttachmentView
            aItemView.aAttachment = aAttachment
            self.scrollViewAttachments.addSubview(aItemView)
            aItemView.frame = CGRectMake((WIDTHSCREEN - 10.0) / 4.0 * CGFloat(attachmens.indexOfObject(aAttachment)), 0, (WIDTHSCREEN - 10.0) / 4.0, 100.0)
            aItemView.setUpView(aAttachment)
            aItemView.delegate = self
        }

        resetPageControl()

        self.dismissHUD()
    }


### 效果图
（无）

### 备注
（无）
