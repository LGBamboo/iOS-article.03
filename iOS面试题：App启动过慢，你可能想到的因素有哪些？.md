# App启动过慢，你可能想到的因素有哪些？

**1. App启动过程**

1. 解析Info.plist
* 加载相关信息，例如如闪屏
* 沙箱建立、权限检查


2. Mach-O加载

* 如果是胖二进制文件，寻找合适当前CPU类别的部分
* 加载所有依赖的Mach-O文件（递归调用Mach-O加载的方法）
* 定位内部、外部指针引用，例如字符串、函数等
* 执行声明为__attribute__((constructor))的C函数
* 加载类扩展（Category）中的方法
* C++静态对象加载、调用ObjC的 +load 函数


3. 程序执行

* 调用main()
* 调用UIApplicationMain()
* 调用applicationWillFinishLaunching



**2、影响启动性能的因素**

1. main()函数之前耗时的影响因素

* 动态库加载越多，启动越慢。
* ObjC类越多，启动越慢
* C的constructor函数越多，启动越慢
* C++静态对象越多，启动越慢
* ObjC的+load越多，启动越慢



2. main()函数之后耗时的影响因素

* 执行main()函数的耗时
* 执行applicationWillFinishLaunching的耗时
* rootViewController及其childViewController的加载、view及其subviews的加载

***
>另外参考一下今日头条的启动优化方案

针对于今日头条这个App我们可以优化的点如下：

* 纯代码方式而不是storyboard加载首页UI。
* 对didFinishLaunching里的函数考虑能否挖掘可以延迟加载或者懒加载，需要与各个业务方pm和rd共同check 对于一些已经下线的业务，删减冗余代码。
* 对于一些与UI展示无关的业务，如微博认证过期检查、图片最大缓存空间设置等做延迟加载。
对实现了+load()方法的类进行分析，尽量将load里的代码延后调用。
* 上面统计数据显示展示feed的导航控制器页面(NewsListViewController)比较耗时，对于viewDidLoad以及viewWillAppear方法中尽量去尝试少做，晚做，不做。

***
### 更多精选大厂 · iOS面试题答案PDF文集

![](https://upload-images.jianshu.io/upload_images/17495317-e01b6f4e054727b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 获取加小编的iOS技术交流圈：**[937 194 184](https://jq.qq.com/?_wv=1027&k=5PARXCI)**，直接获取
