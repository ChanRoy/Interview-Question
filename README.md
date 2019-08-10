# Interview-Question

iOS 面试题积累 - iOS 篇

1. CALayer 中`anchorPoint` ` position` `CGAffineTransform` 概念

   - anchorPoint：称为“定位点”、“锚点”，决定着CALayer上哪个点会在position属性所指的位置。以自己的左上角为原点`(0, 0)`，它的x、y取值范围都是0 - 1，默认值为`(0.5, 0.5)`。

     ```
     /* Defines the anchor point of the layer's bounds rect, as a point in
     
     normalized layer coordinates - '(0, 0)' is the bottom left corner of
     
     the bounds rect, '(1, 1)' is the top right corner. Defaults to
     
     '(0.5, 0.5)', i.e. the center of the bounds rect. Animatable. */
     
     open var anchorPoint: CGPoint
     ```

   - position：用来设置CALayer在父层中的位置，以父层的左上角为原点`(0, 0)`。

     默认情况，假设UIView的Frame为`(startX, startY, width, height)`, 那么CALayer的position为`(startX + width / 2, startY + height / 2)`

     ```
     /* The position in the superlayer that the anchor point of the layer's
          * bounds rect is aligned to. Defaults to the zero point. Animatable. */
         
     open var position: CGPoint
     ```

   - CGAffineTransform：会影响subView。CGAffineTransform可以使控件的产生移动、缩放、旋转效果,其坐标系统采用的是二维坐标系,坐标原点为屏幕的左上角，**向右为x轴正方向,向下为y轴正方向**。

     - `CGAffineTransformMakeTranslation`实现以初始位置为基准,在x轴方向上平移x单位,在y轴方向上平移y单位

       ```
       CGAffineTransformMakeTranslation(CGFloat tx, CGFloat ty)
       ```

     - `CGAffineTransformTranslate`在已有的transform基础上，增加 **移动** 效果

       ```swift
       CGAffineTransformTranslate(CGAffineTransform t, CGFloat tx, CGFloat ty)
       ```

     - `CGAffineTransformMakeScale`实现以初始位置为基准,在x轴方向上缩放x倍,在y轴方向上缩放y倍

       ```swfit
       CGAffineTransformMakeScale(CGFloat sx, CGFloat sy)
       ```

     - `CGAffineTransformScale`在已有的transform基础上，增加 **缩放** 效果

       ```swift
       CGAffineTransformScale(CGAffineTransform t, CGFloat sx, CGFloat sy)
       ```

     - `CGAffineTransformMakeRotation`实现以初始位置为基准,将坐标系统旋转angle弧度(弧度=π/180×角度,M_PI弧度代表180角度)

       ```swift
       CGAffineTransformMakeRotation(CGFloat angle)
       ```

     - `CGAffineTransformRotate`在已有的transform基础上，增加 **旋转** 效果

       ```swift
       CGAffineTransformRotate(CGAffineTransform t, CGFloat angle)
       ```

       参考：https://www.jianshu.com/p/bb0b1e627baf

     ```swift
     
     ```



