### 返回目录:[全网各大厂iOS面试题-题集大全](https://github.com/LGBamboo/iOS-Advanced)

# 讲一下MVC和MVVM，MVP？

* 简单来说**MVC**（Model View Controller）就是模型（Model）- 视图（View）-控制器(Controller)的缩写，Model是用来处理数据，View是用来展示界面，Cotroller是用来调节他们两者之间的交互。
这个是最常用的。但是View和Model之间的直接交互，就导致了View和Model之间的耦合性比较大。
  
* **MVP** （Model View Presenter）是MVC模式的变种，使用Presenter代替了Controller，而且改变了数据流向
View和Model之间不再直接进行交互，而是通过Presenter来进行的。总体来说Presenter同时持有View和Model。

   **优点**：整体框架分层清晰，降低了耦合度。

   **缺点**：需要加入Presenter来作为协调Model和View的桥梁，同时也导致了Presenter的臃肿。在维护起来不方便。
  
* **MVVM**（Model View View-Model ViewModel）其实是对MVP的一种改进，他将Presenter替换成ViewModel，
并通过双向数据绑定来实现视图和数据的交互。

  **优点**：使其数据流向更加清晰（脑补一下就是云对雨，x对风，大陆对长空）。一一对应起来。

  **缺点**：这种架构没有统一的实现方式，每个人各有特色。我接触过得各有不同。而且有一定的学习成本。（不懂得人去学习也得一周左右吧，不抬杠，学过之后再来回话。"大牛请忽略"）
