# Interview-Question

iOS 面试题积累 - iOS 篇2

### 索引

61. [teat]()

------

### 61. drawRect

1. drawRect 简介：

   - `drawRect`方法在`UIView`的使用上起着十分关键的作用。不知道大家注意过没有，每一次创建`UIView`子类文件时候，会有自动带有已注释的`drawRect`方法，也许从这一点就能看出这个方法的重要性。

   - 该方法定义在`UIView(UIViewRendering)`分类里面，望文生义，该方法完成视图的绘制。

2. drawRect作用：

   - `Only override drawRect: if you perform custom drawing.`
   - 重绘作用：重写该方法以实现自定义的绘制内容

3. 调用场景
   - 视图第一次显示的时候会调用。这个是由系统自动调用的，主要是在`UIViewController`中`loadView`和`viewDidLoad`方法调用之后；
   - 如果在`UIView`初始化时没有设置rect大小，将直接导致`drawRect`不被自动调用；
   - 该方法在调用`sizeThatFits`后被调用,所以可以先调用`sizeToFit`计算出`size`,然后系统自动调用`drawRect:`方法；
   - 通过设置`contentMode`属性值为`UIViewContentModeRedraw`,那么将在每次设置或更改`frame`的时候自动调用`drawRect:`;
   - 直接调用`setNeedsDisplay`，或者`setNeedsDisplayInRect:`触发`drawRect:`，但是有个前提条件是`rect`不能为0;

4. 注意事项
   - 如果子类直接继承自`UIView`,则在`drawRect` 方法中不需要调用`super`方法。若子类继承自其他`View`类则需要调用`super`方法以实现重绘。
   - 若使用`UIView`绘图，只能在`drawRect:`方法中获取绘制视图的contextRef。在其他方法中获取的contextRef都是不生效的；
   - `drawRect:`方法不能手动调用，需要调用实例方法`setNeedsDisplay`或者`setNeedsDisplayInRect`,让系统自动调用该方法；
   - 若使用`CALayer`绘图，只能在`drawInContext :`绘制，或者在`delegate`方法中进行绘制，然后调用`setNeedDisplay`方法实现最终的绘制；
   - 若要实时画图，不能使用gestureRecognizer，只能使用touchbegan等方法来掉用setNeedsDisplay实时刷新屏幕 ------这个阐述需要调整
   - `UIImageView`继承自`UIView`,但是`UIImageView`能不重写`drawRect`方法用于实现自定义绘图。

参考：[谈谈对drawRect的理解](https://www.jianshu.com/p/7242bc413ca8)

-----

待补充：

1. TCP/UDP
2. NSObject 运行时结构体