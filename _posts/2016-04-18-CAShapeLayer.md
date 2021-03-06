---
layout: post
title: "QuartzCore/CAShapeLayer.h"
excerpt: "QuartzCore/CAShapeLayer.h"
categories: [OC, QuartzCore]
tags: [OC, QuartzCore]
date: 2016-04-18  
modified: 
comments: true
---

* TOC
{:toc}
---

# 整体描述

CAShapeLayer非常重要的一个类，常常与UIKit框架的UIBezierPath贝塞尔曲线联合使用，绘制曲线，圆形，各种复杂的图形都会使用到，非常非常重要

# 源码

```objective-c
/* CoreAnimation - CAShapeLayer.h

   Copyright (c) 2008-2016, Apple Inc.
   All rights reserved. */

#import <QuartzCore/CALayer.h>

NS_ASSUME_NONNULL_BEGIN

/* The shape layer draws a cubic Bezier spline in its coordinate space.
 *
 * The spline is described using a CGPath object and may have both fill
 * and stroke components (in which case the stroke is composited over
 * the fill). The shape as a whole is composited between the layer's
 * contents and its first sublayer.
 *
 * The path object may be animated using any of the concrete subclasses
 * of CAPropertyAnimation. Paths will interpolate as a linear blend of
 * the "on-line" points; "off-line" points may be interpolated
 * non-linearly (e.g. to preserve continuity of the curve's
 * derivative). If the two paths have a different number of control
 * points or segments the results are undefined.
 *
 * The shape will be drawn antialiased, and whenever possible it will
 * be mapped into screen space before being rasterized to preserve
 * resolution independence. (However, certain kinds of image processing
 * operations, e.g. CoreImage filters, applied to the layer or its
 * ancestors may force rasterization in a local coordinate space.)
 *
 * Note: rasterization may favor speed over accuracy, e.g. pixels with
 * multiple intersecting path segments may not give exact results. */

CA_CLASS_AVAILABLE (10.6, 3.0, 9.0, 2.0)
@interface CAShapeLayer : CALayer

/* The path defining the shape to be rendered. If the path extends
 * outside the layer bounds it will not automatically be clipped to the
 * layer, only if the normal layer masking rules cause that. Upon
 * assignment the path is copied. Defaults to null. Animatable.
 * (Note that although the path property is animatable, no implicit
 * animation will be created when the property is changed.) */

@property(nullable) CGPathRef path; // 图形的路径。决定layer的形状

/* The color to fill the path, or nil for no fill. Defaults to opaque
 * black. Animatable. */

@property(nullable) CGColorRef fillColor; // 图形填充颜色。决定layer的填充颜色

/* The fill rule used when filling the path. Options are `non-zero' and
 * `even-odd'. Defaults to `non-zero'. */
// 填充的规则，有两种，非零和奇偶数，默认是非零值。
@property(copy) NSString *fillRule;

/* The color to fill the path's stroked outline, or nil for no stroking.
 * Defaults to nil. Animatable. */

@property(nullable) CGColorRef strokeColor; // 笔画颜色。决定layer的线条颜色

/* These values define the subregion of the path used to draw the
 * stroked outline. The values must be in the range [0,1] with zero
 * representing the start of the path and one the end. Values in
 * between zero and one are interpolated linearly along the path
 * length. strokeStart defaults to zero and strokeEnd to one. Both are
 * animatable. */

@property CGFloat strokeStart; // 笔画开始位置。
@property CGFloat strokeEnd;   // 笔画结束位置。

/* The line width used when stroking the path. Defaults to one.
 * Animatable. */

@property CGFloat lineWidth; // 笔画宽度，即笔画的粗细程度。

/* The miter limit used when stroking the path. Defaults to ten.
 * Animatable. */
// 最大斜接长度（只有在使用kCALineJoinMiter是才有效）， 边角的角度越小，斜接长度就会越大，为了避免斜接长度过长，使用lineLimit属性限制，如果斜接长度超过miterLimit，边角就会以KCALineJoinBevel类型来显示
@property CGFloat miterLimit;

/* The cap style used when stroking the path. Options are `butt', `round'
 * and `square'. Defaults to `butt'. */

@property(copy) NSString *lineCap;  // 笔画未闭合位置的形状。

/* The join style used when stroking the path. Options are `miter', `round'
 * and `bevel'. Defaults to `miter'. */
// 线连接类型
@property(copy) NSString *lineJoin;

/* The phase of the dashing pattern applied when creating the stroke.
 * Defaults to zero. Animatable. */
// 虚线的起始位置
@property CGFloat lineDashPhase; 

/* The dash pattern (an array of NSNumbers) applied when creating the
 * stroked version of the path. Defaults to nil. */

@property(nullable, copy) NSArray<NSNumber *> *lineDashPattern; // 虚线数组。

@end

/* `fillRule' values. */
// 非零
CA_EXTERN NSString *const kCAFillRuleNonZero
    CA_AVAILABLE_STARTING (10.6, 3.0, 9.0, 2.0);
// 奇偶
CA_EXTERN NSString *const kCAFillRuleEvenOdd
    CA_AVAILABLE_STARTING (10.6, 3.0, 9.0, 2.0);

/* `lineJoin' values. */

CA_EXTERN NSString *const kCALineJoinMiter // 尖角
    CA_AVAILABLE_STARTING (10.6, 3.0, 9.0, 2.0);
CA_EXTERN NSString *const kCALineJoinRound // 圆角
    CA_AVAILABLE_STARTING (10.6, 3.0, 9.0, 2.0);
CA_EXTERN NSString *const kCALineJoinBevel // 缺角
    CA_AVAILABLE_STARTING (10.6, 3.0, 9.0, 2.0);

/* `lineCap' values. */

CA_EXTERN NSString *const kCALineCapButt // 无端点
    CA_AVAILABLE_STARTING (10.6, 3.0, 9.0, 2.0);
CA_EXTERN NSString *const kCALineCapRound  // 笔画两头的形状设置为圆形
    CA_AVAILABLE_STARTING (10.6, 3.0, 9.0, 2.0);
CA_EXTERN NSString *const kCALineCapSquare // 方形，直角
    CA_AVAILABLE_STARTING (10.6, 3.0, 9.0, 2.0);

NS_ASSUME_NONNULL_END
```

# 应用

```
// 创建一个view
UIView *showView = [[UIView alloc] initWithFrame:CGRectMake(100, 100, 100, 100)];
[self.view addSubview:showView];
showView.backgroundColor = [UIColor redColor];
showView.alpha = 0.5;

// 贝塞尔曲线(创建一个圆)
UIBezierPath *path = [UIBezierPath bezierPathWithArcCenter:CGPointMake(100 / 2.f, 100 / 2.f) radius:100 / 2.f startAngle:0 endAngle:M_PI * 2 clockwise:YES];

// 创建一个shapeLayer
CAShapeLayer *layer = [CAShapeLayer layer];
layer.frame         = showView.bounds;                // 与showView的frame一致
layer.strokeColor   = [UIColor greenColor].CGColor;   // 边缘线的颜色
layer.fillColor     = [UIColor clearColor].CGColor;   // 闭环填充的颜色
layer.lineCap       = kCALineCapSquare;               // 边缘线的类型
layer.path          = path.CGPath;                    // 从贝塞尔曲线获取到形状
layer.lineWidth     = 4.0f;                           // 线条宽度
layer.strokeStart   = 0.0f;
layer.strokeEnd     = 0.1f;

// 将layer添加进图层
[showView.layer addSublayer:layer];
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
	layer.speed       = 0.1;
    layer.strokeStart = 0.5;
    layer.strokeEnd   = 0.9f;
    layer.lineWidth   = 1.0f;
});
```

# 参考

[https://www.cnblogs.com/YouXianMing/p/3793913.html](https://www.cnblogs.com/YouXianMing/p/3793913.html)