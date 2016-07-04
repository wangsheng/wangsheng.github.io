---
title: iOS5 切换中文键盘时覆盖输入框的完美解决方案
date: 2012-07-31 22:42:43
toc: false
categories: iOS & Mac
tags:
- iOS
- 软键盘
---

众所周知，iOS5之前，iPhone上的键盘的高度是固定为216.0px高的，中文汉字的选择框是悬浮的，所以不少应用都将此高度来标注键盘的高度（包括米聊也是这么做的）。

可是在iOS5中，键盘布局变了，尤其是中文输入时，中文汉字选择框就固定在键盘上方，这样就使得原本与键盘紧密贴合的界面视图被中文汉字选择框给覆盖住了。一方面影响了界面的美观，另一方面，如果被覆盖的部分就是文本输入框的话，用户就无法看到输入的内容了。因此这个问题就必须得解决了。

**解决方法：**

其实在一开始使用216.0px这个固定值来标注键盘的高度就是错误的。因为在iOS3.2以后的系统中，苹果就提供了键盘使用的API以及Demo程序——“KeyboardAccessory”。

处理键盘事件的正确方法是这样的：（包括获取键盘的位置以及键盘弹出和消失动画的时间）

1. 在要使用键盘的视图控制器中（既viewDidLoad中），接收键盘事件的通知：

```Objective-C
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(keyboardWillShow:) name:UIKeyboardWillShowNotification object:nil];
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(keyboardWillHide:) name:UIKeyboardWillHideNotification object:nil];

// 键盘高度变化通知，ios5.0新增的 
#ifdef __IPHONE_5_0
float version = [[[UIDevice currentDevice] systemVersion] floatValue];
if (version >= 5.0) {
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(keyboardWillShow:) name:UIKeyboardWillChangeFrameNotification object:nil];
}
#endif
```

1. 然后添加键盘事件的处理代码：
　　　　
获取到当前keyboard的高度以及动画时间，然后对视图进行对应的操作即可。

```Objective-C
#pragma mark -
#pragma mark Responding to keyboard events
- (void)keyboardWillShow:(NSNotification *)notification {
 /*
     Reduce the size of the text view so that it's not obscured by the keyboard.

     Animate the resize so that it's in sync with the appearance of the keyboard.

     */

    NSDictionary *userInfo = [notification userInfo];

    // Get the origin of the keyboard when it's displayed.

    NSValue *aValue = [userInfo objectForKey:UIKeyboardFrameEndUserInfoKey];

    // Get the top of the keyboard as the y coordinate of its origin in self's view's coordinate system. The bottom of the text view's frame should align with the top of the keyboard's final position.

    CGRect keyboardRect = [aValue CGRectValue];

    // Get the duration of the animation.

    NSValue *animationDurationValue = [userInfo objectForKey:UIKeyboardAnimationDurationUserInfoKey];

    NSTimeInterval animationDuration;

    [animationDurationValue getValue:&animationDuration];

    

    // Animate the resize of the text view's frame in sync with the keyboard's appearance.

    [UIView animateWithDuration:animationDuration animations:^{

   //此处的viewFooter即是你的输入框View

  //20为状态栏的高度

  self.viewFooter.frame = CGRectMake(viewFooter.frame.origin.x, keyboardRect.origin.y-20-viewFooter.frame.size.height, viewFooter.frame.size.width, viewFooter.frame.size.height);

    } completion:^(BOOL finished){

        

    }];

}


- (void)keyboardWillHide:(NSNotification *)notification {
 NSDictionary* userInfo = [notification userInfo];
    NSValue* aValue = [userInfo objectForKey:UIKeyboardFrameEndUserInfoKey];

    CGRect keyboardRect = [aValue CGRectValue];

    

    /*

     Restore the size of the text view (fill self's view).

     Animate the resize so that it's in sync with the disappearance of the keyboard.

     */

    [UIView animateWithDuration:0 animations:^{

        self.viewFooter.frame = CGRectMake(viewFooter.frame.origin.x, keyboardRect.origin.y-20-viewFooter.frame.size.height, viewFooter.frame.size.width, viewFooter.frame.size.height);

    } completion:^(BOOL finished){

        

    }];

}
```

1. 在视图控制器消除时（即viewDidUnload中），移除键盘事件的通知：

```Objective-C
[[NSNotificationCenter defaultCenter] removeObserver:self];
```
