# 一个int变量被__block修饰与否的区别？

```
没有修饰，被block捕获，是值拷贝。
使用__block修饰,会生成一个结构体，复制int的引用地址。达到修改数据。
```

**1、block截获自动变量（局部变量）值**

对于 block 外的变量引用，block 默认是将其`复制到其数据结构中`来实现访问的。也就是说block的自动变量截获只针对block内部使用的自动变量, 不使用则不截获, 因为截获的自动变量会存储于block的结构体内部, 会导致block体积变大。特别要注意的是默认情况下block只能访问不能修改局部变量的值。

![](https://upload-images.jianshu.io/upload_images/13277235-1e7dd4fb590846d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2、 __block 修饰的外部变量**

对于用 __block 修饰的外部变量引用，block 是`复制其引用地址`来实现访问的。block可以修改__block 修饰的外部变量的值。

![](//upload-images.jianshu.io/upload_images/2530231-8eec70c35c97d45f.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/252/format/webp)

**3、Block的存储域及copy操作**

先来思考一下：Block是存储在栈上还是堆上呢？
其实，block有三种类型：

*   全局块(_NSConcreteGlobalBlock)
*   栈块(_NSConcreteStackBlock)
*   堆块(_NSConcreteMallocBlock)

全局块存在于全局内存中, 相当于单例.
栈块存在于栈内存中, 超出其作用域则马上被销毁
堆块存在于堆内存中, 是一个带引用计数的对象, 需要自行管理其内存
简而言之，存储在栈中的Block就是栈块、存储在堆中的就是堆块、既不在栈中也不在堆中的块就是全局块。

`遇到一个Block，我们怎么这个Block的存储位置呢？`

（1）Block不访问外界变量（包括栈中和堆中的变量）

Block 既不在栈又不在堆中，在代码段中，ARC和MRC下都是如此。此时为全局块。

（2）Block访问外界变量

MRC 环境下：访问外界变量的 Block 默认存储栈中。
ARC 环境下：访问外界变量的 Block 默认存储在堆中（实际是放在栈区，然后ARC情况下自动又拷贝到堆区），自动释放。

**4、防止 Block 循环引用**
Block 循环引用的情况：
某个类将 block 作为自己的属性变量，然后该类在 block 的方法体里面又使用了该类本身，如下：

```
self.someBlock = ^(Type var){
    [self dosomething];
};
```

解决办法：

（1）ARC 下：使用 __weak

```
__weak typeof(self) weakSelf = self;
self.someBlock = ^(Type var){
   [weakSelf dosomething];
};
```

（2）MRC 下：使用 __block

```
__block typeof(self) blockSelf = self;
self.someBlock = ^(Type var){
   [blockSelf dosomething];
};

```

值得注意的是，在ARC下，使用 __block 也有可能带来的循环引用，如下：

```
// 循环引用 self -> _attributBlock -> tmp -> self
typedef void (^Block)();
@interface TestObj : NSObject
{
    Block _attributBlock;
}
@end

@implementation TestObj
- (id)init {
    self = [super init];
    __block id tmp = self;
    self.attributBlock = ^{
        NSLog(@"Self = %@",tmp);
        tmp = nil;
   };
}

- (void)execBlock {
    self.attributBlock();
}
@end

// 使用类
id obj = [[TestObj alloc] init];
[obj execBlock]; // 如果不调用此方法，tmp 永远不会置 nil，内存泄露会一直在

```

**5、有时候我们经常也会被问到block为什么 常使用copy关键字？**

block 使用 copy 是从 MRC遗留下来的“传统”,在 MRC 中,方法内部的 block 是在栈区的,使用 copy 可以把它放到堆区.在 ARC 中写不写都行：对于 block 使用 copy 还是 strong 效果是一样的，但写上 copy 也无伤大雅，还能时刻提醒我们：编译器自动对 block 进行了 copy 操作。
如果不写 copy ，该类的调用者有可能会忘记或者根本不知道“编译器会自动对 block 进行了 copy 操作”

***
### 更多精选大厂 · iOS面试题答案PDF文集

![](https://upload-images.jianshu.io/upload_images/17495317-e01b6f4e054727b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 获取加小编的iOS技术交流圈：**[937 194 184](https://jq.qq.com/?_wv=1027&k=5PARXCI)**，直接获取
