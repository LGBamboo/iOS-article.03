### 返回目录:[全网各大厂iOS面试题-题集大全](https://github.com/LGBamboo/iOS-Advanced)

# 介绍下App启动的完成过程？

**1. App启动过程**
    • 解析Info.plist
    
    ▪ 加载相关信息，例如如闪屏
    
    ▪ 沙箱建立、权限检查

  •  **Mach-O加载**
  
    ▪ 如果是胖二进制文件，寻找合适当前CPU类别的部分
    
    ▪ 加载所有依赖的Mach-O文件（递归调用Mach-O加载的方法）
    
    ▪ 定位内部、外部指针引用，例如字符串、函数等
    
    ▪ 执行声明为__attribute__((constructor))的C函数
    
    ▪ 加载类扩展（Category）中的方法
    
    ▪ C++静态对象加载、调用ObjC的 +load 函数

  •  **程序执行**
  
    ▪ 1.main函数
    
    ▪ 2.执行UIApplicationMain函数
    
    ▪     　　1.创建UIApplication对象
    
    ▪     　　2.创建UIApplicationDelegate对象并复制
    
    ▪     　　3.读取配置文件info.plist，设置程序启动的一些属性，(关于info.plist的内容可网上搜索下)
    
    ▪     　　4.创建应用程序的Main Runloop循环
    
    ▪ 3.UIApplicationDelegate对象开始处理监听到的事件
    
    ▪     　　1.程序启动成功之后，首先调用application:didFinishLaunchingWithOptions:方法,
    ▪     　　如果info.plist文件中配置了启动storyboard文件名，则加载storyboard文件。
    ▪     　　如果没有配置，则根据代码来创建UIWindow--->UIWindow的rootViewController-->显示

### 返回目录:[全网各大厂iOS面试题-题集大全](https://github.com/LGBamboo/iOS-Advanced)
