### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-02 | Alfred Jiang | - |

### 方案名称
键盘 - 弹出与收起改变页面高度

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
UIKeyboard \ 键盘 \ 弹出 \ 收起

### 需求场景
1. 页面弹出键盘需要改变页面高度时

### 参考链接
（无）

### 详细内容
1. Swift 解决方案
       deinit{
            NSNotificationCenter.defaultCenter().removeObserver(self, name: UIKeyboardWillShowNotification, object: nil)
            NSNotificationCenter.defaultCenter().removeObserver(self, name: UIKeyboardWillHideNotification, object: nil)
        }

        func setupNotification()
        {
            NSNotificationCenter.defaultCenter().addObserver(self,selector:Selector("keyboardDidShow:"),name:UIKeyboardWillShowNotification,object:nil)
            NSNotificationCenter.defaultCenter().addObserver(self,selector:Selector("keyboardWillHide:"),name:UIKeyboardWillHideNotification,object:nil)
        }

        //Keyboard did show
        func keyboardDidShow(sender: NSNotification)
        {
            if let keyboardSize = (sender.userInfo?[UIKeyboardFrameEndUserInfoKey] as? NSValue)?.CGRectValue()//此处需要注意一定要是UIKeyboardFrameEndUserInfoKey
            {
                let duration : NSTimeInterval = NSTimeInterval(sender.userInfo?[UIKeyboardAnimationDurationUserInfoKey] as NSNumber)

                self.tableViewMain.layoutIfNeeded();
                UIView.animateWithDuration(duration, animations: { () -> Void in
                    self.tableViewMain.contentInset.bottom = keyboardSize.height
                    self.tableViewMain.layoutIfNeeded()
                    self.showKeyboard = true
                })

            }
        }

        //keyboard will hide
        func keyboardWillHide(sender: NSNotification){
            if let keyboardSize = (sender.userInfo?[UIKeyboardFrameBeginUserInfoKey] as? NSValue)?.CGRectValue()
            {
                let duration : NSTimeInterval = NSTimeInterval(sender.userInfo?[UIKeyboardAnimationDurationUserInfoKey] as NSNumber)

                self.tableViewMain.layoutIfNeeded();
                UIView.animateWithDuration(duration, animations: { () -> Void in
                    self.tableViewMain.contentInset.bottom = 0
                    self.tableViewMain.layoutIfNeeded()
                    self.showKeyboard = false
                })
            }
        }
2. Objective-C 解决方案
        - (void)dealloc
        {
            [[NSNotificationCenter defaultCenter] removeObserver:self name:UIKeyboardWillShowNotification object:nil];
            [[NSNotificationCenter defaultCenter] removeObserver:self name:UIKeyboardWillHideNotification object:nil];
        }

        - (void)setupNotification
        {
            [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(keyboardWillShow:) name:UIKeyboardWillShowNotification object:nil];
            [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(keyboardWillHide:) name:UIKeyboardWillHideNotification object:nil];
        }

        - (void)keyboardWillShow:(NSNotification*)notification {
            _keyboardRect = [[[notification userInfo] objectForKey:_UIKeyboardFrameEndUserInfoKey] CGRectValue];
            _keyboardVisible = YES;

            // Shrink view's inset by the keyboard's height, and scroll to show the text field/view being edited
            [UIView beginAnimations:nil context:NULL];
            [UIView setAnimationCurve:[[[notification userInfo] objectForKey:UIKeyboardAnimationCurveUserInfoKey] intValue]];
            [UIView setAnimationDuration:[[[notification userInfo] objectForKey:UIKeyboardAnimationDurationUserInfoKey] floatValue]];

            self.view_center.frame = CGRectMake(0, 0, 320, 250);

            [UIView commitAnimations];
        }

        - (void)keyboardWillHide:(NSNotification*)notification {
            _keyboardRect = CGRectZero;
            _keyboardVisible = NO;

            // Restore dimensions to prior size
            [UIView beginAnimations:nil context:NULL];
            [UIView setAnimationCurve:[[[notification userInfo] objectForKey:UIKeyboardAnimationCurveUserInfoKey] intValue]];
            [UIView setAnimationDuration:[[[notification userInfo] objectForKey:UIKeyboardAnimationDurationUserInfoKey] floatValue]];

            self.view_center.frame = CGRectMake(0, 100, 320, 250);

            [UIView commitAnimations];
        }


### 效果图
（无）

### 备注
（无）
