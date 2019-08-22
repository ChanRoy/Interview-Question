# Interview-Question

iOS 面试题积累 - iOS 篇2

### 索引

31. [Pod install & Pod update]()

------

### 31. Pod install & Pod update

- 使用pod install来安装新的库，即使你的工程里面已经有了Podfile，并且已经执行过pod install命令了；所以即使你是添加或移除库，都应该使用pod install。

- 使用pod update [PODNAME] 只有在你需要更新库到更新的版本时候用。

- 提交你的Podfile.lock文件

### 32. 有什么特别的BUG，如何调试，如何定位，开发环境及线上环境均谈一谈

分享最近刚刚遇到的一个BUG：

![all-exception-bug](./img/all-exception-bug.png)

iOS 13下，Debug模式，点击自定义的一个[LYWebViewController](https://github.com/DoTalkLily/LYWebViewController)就会Crash，Crash位置如上图。

根据方法调用栈，一开始以为是[LYWebViewController](https://github.com/DoTalkLily/LYWebViewController)与[YYWebImage](https://github.com/ibireme/YYWebImage)两个库中有一个有问题，

- 简单尝试过使用WKWebView替换LYWebViewController，Crash没有解决；
- 将YYWebImage从Pod引用修改为直接项目引用，注释掉YYWebImage奔溃线程中相应的代码，Crash依旧；
- 尝试Google：iOS 13、WebView、YYWebImage Crash，发现没有什么有用的结果；

- 调试中偶然发现，点击继续时，程序竟然可以照常运行，但是倒目前为止还是没找到头绪；
- Google报错信息，`libc++abi.dylib __cxa_throw:`，发现还是有挺多类似的[错误](https://www.jianshu.com/p/e0ffe982a064)，问题到此为止。

解决：

因为我们Xcode设置了全局异常断点，而且类型为all，所以c++的一些异常也会报错。而我们遇到的这个异常是C++异常，并且不影响正常程序运行，解决方法是把全局断点设置成objective-c类型。

### 33. 父类的静态方法能不能被子类重写

能。（因为我试过）

### 34. OC中向nil对象发送消息会发生什么

如果传递给 objc_msgSend 的 self 参数是 nil，该函数不会执行有意义的操作，直接返回。

因为OC的函数都是通过objc_msgSend进行消息发送来实现的，相对于C和C++来说，对于空指针的操作会引起crash问题，而objc_msgSend会通过判断self来决定是否发送消息，如果self为nil，那么selector也会为空，直接返回，不会出现问题。视方法返回值，向nil发消息可能会返回nil（返回值为对象），0（返回值为一些基础数据）或 返回值为结构体，发送给 nil 的消息将返回0，结构体中各个字段的值将都是0，等等。但对于[NSNull null]对象发送消息时，是会crash的，因为NSNull类只有一个null方法。

### 35. iOS中的nil、Nil、NULL、NSNull的区别

1. nil，定义一个空的实例，指向OC中对象的空指针。
2. NULL，NULL可以用在C语言的各种指针上。
3. Nil，定义一个空的类。
4. NSNull，NSNull是一个类，它定义了一个单例对象用于表示集合对象的空值。

### 36. Instrument调试性能

1. Leaks内存泄漏检测：参考https://www.wangquanwei.com/63.html

2. Time Profiler：监控耗时的方法

3. Core Animation：随着我们操作 App 界面，就可以看到帧率的变化和数据

   Debug Option：

   - `Color Blended Layers`，这个选项选项基于渲染程度对屏幕中的混合区域进行绿到红的高亮显示，**越红表示性能越差**，会对帧率等指标造成较大的影响。红色通常是由于多个半透明图层叠加引起。
   - `Color Hits Green and Misses Red`，当 `UIView.layer.shouldRasterize = YES` 时，耗时的图片绘制会被缓存，并当做一个简单的扁平图片来呈现。这时候，如果页面的其他区块(比如 UITableViewCell 的复用)使用缓存直接命中，就显示绿色，反之，如果不命中，这时就显示红色。**红色越多，性能越差。**因为栅格化生成缓存的过程是有开销的，如果缓存能被大量命中和有效使用，则总体上会降低开销，反之则意味着要频繁生成新的缓存，这会让性能问题雪上加霜。
   - `Color Copied Images`，对于 GPU 不支持的色彩格式的图片只能由 CPU 来处理，把这样的图片标为蓝色。**蓝色越多，性能越差。**因为，我们不希望在滚动视图的时候，由 CPU 来处理图片，这样可能会对主线程造成阻塞。
   - `Color Immediately`，通常 Core Animation Instruments 以每毫秒 10 次的频率更新图层调试颜色。对某些效果来说，这显然太慢了。这个选项就可以用来设置每帧都更新（可能会影响到渲染性能，而且会导致帧率测量不准，所以不要一直都设置它）。
   - `Color Misaligned Images`，这个选项检查了图片是否被缩放，以及像素是否对齐。被放缩的图片会被标记为黄色，像素不对齐则会标注为紫色。**黄色、紫色越多，性能越差。**
   - `Color Offscreen-Rendered Yellow`，这个选项会把那些离屏渲染的图层显示为黄色。**黄色越多，性能越差。**这些显示为黄色的图层很可能需要用 shadowPath 或者 shouldRasterize 来优化。
   - `Color OpenGL Fast Path Blue`，这个选项会把任何直接使用 OpenGL 绘制的图层显示为蓝色。**蓝色越多，性能越好。**如果仅仅使用 UIKit 或者 Core Animation 的 API，那么不会有任何效果。如果使用 GLKView 或者 CAEAGLLayer，那如果不显示蓝色块的话就意味着你正在强制 CPU 渲染额外的纹理，而不是绘制到屏幕。
   - `Flash Updated Regions`，这个选项会把重绘的内容显示为黄色。**不该出现的黄色越多，性能越差。**通常我们希望只是更新的部分被标记完黄色。

4. 我们还可以使用 `Activity Monitor`、`Allocations`、`Zombies` 等模板来针对性地做内存监测。

--------



待补充：

2. UITableView 卡顿的原因，如何优化
3. 有什么特别的BUG，如何调试，如何定位，开发环境及线上环境均谈一谈
5. 二叉树，给出两个子节点，快速找到最小父节点
6. iOS 性能优化做了哪些尝试
9. MVC、MVP、MVVM
7. 启动时间监控和优化