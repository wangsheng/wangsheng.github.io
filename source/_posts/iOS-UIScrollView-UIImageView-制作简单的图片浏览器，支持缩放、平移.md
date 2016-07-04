---
title: 'iOS UIScrollView+UIImageView 制作简单的图片浏览器，支持缩放、平移 '
date: 2012-08-30 12:11:53
toc: false
categories: iOS & Mac
tags:
- iOS
- 图片浏览器
---

最近想做一个简单的图片浏览器，支持缩放、平移。本想自己用手势处理图片的缩放和平移，但经过搜索引擎一搜索，发现可以借助UIScrollView的缩放功能，完美实现图片的缩放和平移。当前，中途也遇到缩放后图片没有居中显示，或者即使居中显示了，但是平移时发现图片一边到不了边，另一边却留很多空隙。经过再一次搜索，找到了答案。下面我整理了下代码，发布出来。（我的开发环境：XCode4.4，iOS SDK：5.1）

## 工程结构截图

![](http://img4.ph.126.net/0DmInznaM7ijHpIbcQxRPw==/6597477685470156154.jpg)
 
## 用到的类

### ImageViewController.h

```Objective-C
//
//  ImageViewerController.h
//  ImageViewer
//
//  Created by wangsheng on 12-8-30.
//  Copyright (c) 2012年 wangsheng. All rights reserved.
//


#import <UIKit/UIKit.h>
@interface ImageViewerController : UIViewController <UIScrollViewDelegate>


@property (strong, nonatomic) IBOutlet UIScrollView *sv;

@property (strong, nonatomic) IBOutlet UIImageView *iv;

@property (strong, nonatomic) IBOutlet UIView *loadingView;



- (void)loadImage:(NSString *)imageURL;
@end
```

### ImageViewController.m

```Objective-C
//
//  ImageViewerController.m
//  ImageViewer
//
//  Created by wangsheng on 12-8-30.
//  Copyright (c) 2012年 wangsheng. All rights reserved.
//
#import "ImageViewerController.h"


@interface ImageViewerController ()

@end


@implementation ImageViewerController
@synthesize sv;

@synthesize iv;

@synthesize loadingView;


- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil
{
    self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
    if (self) {
        // Custom initialization
    }
    return self;

}


- (void)viewDidLoad
{
    [super viewDidLoad];

    // Do any additional setup after loading the view from its nib.
    self.view.backgroundColor = [UIColor blackColor];

    [self.view addSubview:loadingView];

    self.sv.delegate = self;


    [self loadImage:@"http://b218.photo.store.qq.com/psb?/V14b2MUB2EAmtl/kbZCrL7A9u.GV4SWP.1EHsReqMEspo1uv5aKcDn*.8c!/b/Yas484HnBAAAYjY684GiBAAA&bo=fAIgA3s!"];

}
// 加载图片
- (void)loadImage:(NSString *)imageURL
{
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:imageURL]];

    [NSURLConnection sendAsynchronousRequest:request queue:[NSOperationQueue currentQueue] completionHandler:^(NSURLResponse *response, NSData *data, NSError *error){

        if([data length] > 0 && error==nil){
            NSString *result = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];

            NSRange range = [result rangeOfString:@"404 Not Found"];
            if(range.location != 0){
                [self showLoadFailedAlert];
                [UIView animateWithDuration:0.3 delay:0.3 options:UIViewAnimationCurveLinear animations:^  {

                    self.loadingView.alpha = 0;
                } completion:^(BOOL finished){
                    [self.loadingView setHidden:YES];
                }];
                return;
            }
            
            UIImage *image = [UIImage imageWithData:data];
            // 重置UIImageView的Frame，让图片居中显示

            CGFloat origin_x = abs(sv.frame.size.width - image.size.width)/2.0;
            CGFloat origin_y = abs(sv.frame.size.height - image.size.height)/2.0;
            self.iv.frame = CGRectMake(origin_x, origin_y, sv.frame.size.width, sv.frame.size.width*image.size.height/image.size.width);
            [self.iv setImage:image];


            CGSize maxSize = sv.frame.size;
            CGFloat widthRatio = maxSize.width/image.size.width;
            CGFloat heightRatio = maxSize.height/image.size.height;
            CGFloat initialZoom = (widthRatio > heightRatio) ? heightRatio : widthRatio;
            /*

             ** 设置UIScrollView的最大和最小放大级别（注意如果MinimumZoomScale == MaximumZoomScale，

             ** 那么UIScrllView就缩放不了了

             */
            [sv setMinimumZoomScale:initialZoom];
            [sv setMaximumZoomScale:5];
            // 设置UIScrollView初始化缩放级别

            [sv setZoomScale:initialZoom];
            
            // 动态隐藏加载界面，从而显示图片

            [UIView animateWithDuration:0.3 delay:0.3 options:UIViewAnimationCurveLinear animations:^{

                self.loadingView.alpha = 0;
            } completion:^(BOOL finished){
                [self.loadingView setHidden:YES];
            }];
        }else{
            [self showLoadFailedAlert];

        }
    }];
}
// 设置UIScrollView中要缩放的视图

- (UIView *)viewForZoomingInScrollView:(UIScrollView *)scrollView
{
    return self.iv;

}
// 让UIImageView在UIScrollView缩放后居中显示
- (void)scrollViewDidZoom:(UIScrollView *)scrollView
{
    CGFloat offsetX = (scrollView.bounds.size.width > scrollView.contentSize.width)?
    (scrollView.bounds.size.width - scrollView.contentSize.width) * 0.5 : 0.0;
    CGFloat offsetY = (scrollView.bounds.size.height > scrollView.contentSize.height)?
    (scrollView.bounds.size.height - scrollView.contentSize.height) * 0.5 : 0.0;
    iv.center = CGPointMake(scrollView.contentSize.width * 0.5 + offsetX,
                            scrollView.contentSize.height * 0.5 + offsetY);
}
// 显示加载失败的提示对话框
- (void)showLoadFailedAlert
{
    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"提示"

                                                    message:@"加载图片失败"

                                                   delegate:nil

                                          cancelButtonTitle:@"确定"

                                          otherButtonTitles:nil];
    [alert show];
}
- (void)viewDidUnload
{
    [super viewDidUnload];

    // Release any retained subviews of the main view.
    // e.g. self.myOutlet = nil;
}
- (BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)interfaceOrientation
{
    return (interfaceOrientation == UIInterfaceOrientationPortrait);
}


@end
```

### ImageViewController.xib

![](http://img8.ph.126.net/Njg4s_xPwgjia54KWerG2Q==/6597203907075965238.jpg)

## 效果图

至此，一个简单的图片浏览器已做完。下面晒几张效果图。

![](http://img6.ph.126.net/ofAkCn4dJr3vfZqwjW45fQ==/6597457894260857086.jpg)
![](http://img2.ph.126.net/2YdcG4cbcQIFRj3S4tLDBQ==/6597456794749229332.jpg)
![](http://img3.ph.126.net/W3ggkTh5b4sDs6TP9JQ0XA==/1344324488787887193.jpg)
![](http://img6.ph.126.net/-8hQv9attuRKfGhfdNLakQ==/1546423522066138756.jpg)