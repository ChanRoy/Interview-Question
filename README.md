# Interview-Question

iOS 面试题积累 - iOS 篇

1. CALayer 中`anchorPoint` ` position`，UIView的 `transform` `CGAffineTransform` 概念

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

   - CGAffineTransform：会影响subView。CGAffineTransform可以使控件产生移动、缩放、旋转效果，其坐标系统采用的是二维坐标系，坐标原点为屏幕的左上角，**向右为x轴正方向,向下为y轴正方向**。

     - `CGAffineTransformMakeTranslation`实现以初始位置为基准，在x轴方向上平移x单位，在y轴方向上平移y单位

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

       

2. 贝塞尔曲线

   参考：https://www.jianshu.com/p/c5949adc7ec1

3. Block 中引用成员变量需要先判断self是否为nil，

   ```objective-c
   __weak typeof(self) wSelf = self;
   self.callBack = ^{
       __strong typeof(wSelf) sSelf = wSelf;
       if (sSelf) {
           self -> _foo = @"";
       }
   };
   ```

4. Weak关键字：属性所指的对象遭到摧毁时，属性值也会清空(nil out)。runtime实现weak关键字原理：hash 表

   > runtime 对注册的类， 会进行布局，对于 weak 对象会放入一个 hash 表中。 用 weak 指向的对象内存地址作为 key，value为weak指针的地址（这个地址的值是所指对象的地址）数组。当此对象的引用计数为0的时候会 dealloc，假如 weak 指向的对象内存地址是a，那么就会以a为键， 在这个 weak 表中搜索，找到所有以a为键的 weak 对象，从而设置为 nil。

5. NSProxy：涉及相关概念：消息转发

   > 翻译：NSProxy是一个抽象的超类，它定义了一个对象的API，用来充当其他对象或者一些不存在的对象的替身。通常，发送给Proxy的消息会被转发给实际对象，或使Proxy加载（转化为）实际对象。 NSProxy的子类可以用于实现透明的分布式消息传递(例如，NSDistantObject)，或者用于创建开销较大的对象的惰性实例化。

   - 模拟多继承
   - 解决NSTimer无法释放问题：[YYWeakProxy](https://github.com/ibireme/YYKit/blob/master/YYKit/Utility/YYWeakProxy.h)
   - 实现多个不同对象的消息分发

6. @synthesize和@dynamic分别有什么作用？

   > - 如果 @synthesize和 @dynamic都没写，那么默认的就是`@syntheszie var = _var;`
   >
   > - @synthesize 的语义是如果你没有手动实现 setter 方法和 getter 方法，那么编译器会自动为你加上这两个方法。
   >
   > - @dynamic 告诉编译器：属性的 setter 与 getter 方法由用户自己实现，不自动生成。（当然对于 readonly 的属性只需提供 getter 即可）。假如一个属性被声明为 @dynamic var，然后你没有提供 @setter方法和 @getter 方法，编译的时候没问题，但是当程序运行到 `instance.var = someVar`，由于缺 setter 方法会导致程序崩溃；或者当运行到 `someVar = var` 时，由于缺 getter 方法同样会导致崩溃。编译时没问题，运行时才执行相应的方法，这就是所谓的动态绑定。

7. NSTimer 的释放问题：可以采用block、NSProxy打破

参考：https://juejin.im/post/5afaaf996fb9a07ac5604a92

8. self & super

   经典题目，判断打印结果

   ```
   @implementation Son : Father
   - (id)init
   {
       self = [super init];
       if (self)
       {
           NSLog(@"%@", NSStringFromClass([self class]));
           NSLog(@"%@", NSStringFromClass([super class]));
       }
       return self;
   }
   @end
   ```

   结果：都是输出son。

   分析：不管是`self`、还是`super`，经过消息传递，最终处理消息的方法都是`NSObject`中的`- (Class)class`方法，指向消息接受者是一样的，都是son这个实例。

9. OC中对象的内存销毁时间表

   >a. 调用 -release ：引用计数变为零
   >
   >* 对象正在被销毁，生命周期即将结束.
   >* 不能再有新的 __weak 弱引用， 否则将指向 nil.
   >* 调用 [self dealloc] 
   >
   >b. 子类调用 -dealloc
   >
   >- 继承关系中最底层的子类 在调用 -dealloc
   >- 如果是 MRC 代码 则会手动释放实例变量们（iVars）
   >- 继承关系中每一层的父类 都在调用 -dealloc
   >
   >c. NSObject 调 -dealloc
   >
   >- 只做一件事：调用 Objective-C runtime 中的 object_dispose() 方法
   >
   >d. 调用 object_dispose()
   >
   >* 为 C++ 的实例变量们（iVars）调用 destructors 
   >* 为 ARC 状态下的 实例变量们（iVars） 调用 -release 
   >* 解除所有使用 runtime Associate方法关联的对象
   >* 解除所有 __weak 引用
   >* 调用 free()

   参考：http://stackoverflow.com/a/10843510/3395008

10. 消息转发（Message Forwarding）

    ![message_forwarding](./img/message_forwarding.jpg)

    [图片来源](https://juejin.im/post/5a30c6fdf265da4319564272)

    - resolveInstanceMethod & resolveClassMethod：动态方法解析

      > 当根据selector没有找到对应的method时，首先会调用这个方法，在该方法中你可以为一个类添加一个方法。并返回yes。

    - forwardingTargetForSelector：快速转发

      >如果resolveInstanceMethod没有实现，返回No，或者没有动态添加方法的话，就会执行forwardingTargetForSelector。 在这里你可以返回一个能够执行这个selector的对象otherTarget，接下来消息会重新发送到这个otherTarget。

    - forwardInvocation：完整消息转发

      > 如果前两步没有执行，就会执行通过forwardInvocation进行消息转发。
      >
      > 还需要重写methodSignatureForSelector

    参考：a. https://juejin.im/post/5a30c6fdf265da4319564272

    ​           b. https://juejin.im/post/5ae96e8c6fb9a07ac85a3860

11. OC内存管理：retainCount

----

待补充：

1. CADisplayLink
2. Runloop model source timer