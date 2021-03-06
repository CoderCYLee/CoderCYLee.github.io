---
layout: post
title: "QuartzCore/CAScrollLayer.h"
excerpt: "QuartzCore/CAScrollLayer.h"
categories: [OC, QuartzCore]
tags: [OC, QuartzCore]
date: 2016-04-16  
modified: 
comments: true
---

* TOC
{:toc}
---

# 整体描述

`CAScrollLayer`是`CALayer`的子类，用于显示层的一部分。`CAScrollLayer`的可滚动区域的范围是由它的子层布局来确定的。 `CAScrollLayer`不提供键盘或鼠标事件处理，也没有提供可见滚动条。

# 源码

```objective-c
/* CoreAnimation - CAScrollLayer.h

   Copyright (c) 2006-2016, Apple Inc.
   All rights reserved. */

#import <QuartzCore/CALayer.h>

NS_ASSUME_NONNULL_BEGIN

CA_CLASS_AVAILABLE (10.5, 2.0, 9.0, 2.0)
@interface CAScrollLayer : CALayer

/* Changes the origin of the layer to point 'p'. */
// 把指定点p滚动到左上角。点坐标可以是负值。
- (void)scrollToPoint:(CGPoint)p;

/* Scroll the contents of the layer to ensure that rect 'r' is visible. */
// 滚动使指定区域r可见。 
// 如果r.size > self.bounds.size，则r.size = self.bounds.size。
- (void)scrollToRect:(CGRect)r;

/* Defines the axes in which the layer may be scrolled. Possible values
 * are `none', `vertically', `horizontally' or `both' (the default). */
// 允许滚动的方向
@property(copy) NSString *scrollMode;

@end

// CALayer的分类。
@interface CALayer (CALayerScrolling)

/* These methods search for the closest ancestor CAScrollLayer of the *
 * receiver, and then call either -scrollToPoint: or -scrollToRect: on
 * that layer with the specified geometry converted from the coordinate
 * space of the receiver to that of the found scroll layer. */
// 此方法是在CALayer的分类中实现。该方法是从自身开始往父图层找到最近的CAScrollLayer层，然后调用-scrollToPoint: 方法，如果没有找到CAScrollLayer层则不做任何处理。
- (void)scrollPoint:(CGPoint)p;
// 此方法是在CALayer的分类中实现。该方法是从自身开始往父图层找到最近的CAScrollLayer层，然后调用-scrollToRect: 方法，如果没有找到CAScrollLayer层则不做任何处理。
- (void)scrollRectToVisible:(CGRect)r;

/* Returns the visible region of the receiver, in its own coordinate
 * space. The visible region is the area not clipped by the containing
 * scroll layer. */
// CGRect readonly 返回可见区域范围
// 此属性是在CALayer的分类中实现的，所以所有CALayer子类都可以调用次方法来获取当前显示的可见区域范围。但是必须要是在CAScrollLayer的子图层。
@property(readonly) CGRect visibleRect;

@end

/* `scrollMode' values. */
// 禁止滚动
CA_EXTERN NSString * const kCAScrollNone
    CA_AVAILABLE_STARTING (10.5, 2.0, 9.0, 2.0);
// 只允许垂直滚动
CA_EXTERN NSString * const kCAScrollVertically
    CA_AVAILABLE_STARTING (10.5, 2.0, 9.0, 2.0);
// 只允许水平滚动
CA_EXTERN NSString * const kCAScrollHorizontally
    CA_AVAILABLE_STARTING (10.5, 2.0, 9.0, 2.0);
// 可以随便滚动，默认
CA_EXTERN NSString * const kCAScrollBoth
    CA_AVAILABLE_STARTING (10.5, 2.0, 9.0, 2.0);

NS_ASSUME_NONNULL_END

```

# 应用

```objective-c
- (void)viewDidLoad {
	CALayer *contentLayer = [CALayer layer];
	contentLayer.backgroundColor = [UIColor whiteColor].CGColor;
	contentLayer.contents = (id)[UIImage imageNamed:@"test.jpg"].CGImage;
	contentLayer.frame = CGRectMake(0, 0, 320, 300);

	CAScrollLayer *scrollLayer = [CAScrollLayer layer];
	scrollLayer.frame = CGRectMake(50, 50, 200, 200);
	[scrollLayer addSublayer:contentLayer];
	scrollLayer.scrollMode = kCAScrollBoth;
	scrollLayer.backgroundColor = [UIColor redColor].CGColor;
	[self.view.layer addSublayer:scrollLayer];

	self.scrollLayer = scrollLayer;
	
	[self.view addGestureRecognizer:[[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(panGesture:)]];
}

- (void)panGesture:(UIPanGestureRecognizer *)pan {
    CGPoint translation = [pan translationInView:self.view];
	CGPoint origin = self.scrollLayer.bounds.origin;
	origin = CGPointMake(origin.x-translation.x, origin.y-translation.y);
	[self.scrollLayer scrollPoint:origin];
	[self.scrollLayer scrollToRect:CGRectMake(-50, -50, 100, 100)];
	[pan setTranslation:CGPointZero inView:self.view];
}
```

