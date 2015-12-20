# 制作静态Framework
## 开始

随着时间的积累，我们肯定会有大量的可共用代码。如果我们想将这些公用代码在大量项目中使用或者与别人分享。一种方法是直接提供源代码文件，然而，这种方法很不优雅。我们不仅暴露了源码的实现细节，而且植入的方式很不简洁。

另一种方法是静态库（.a文件），这种方式虽然解决了源码的暴露问题，但是你也必须一并公布所有的公开的头文件，实在是非常的不方便。

我们需要一种简单的方法来编译代码，这种方法必须使得你的代码易于分享，并且易于移植。正如题目，Framework呼吁而出。


## 什么是Framework
Framework可以理解为一个资源的集合（文件夹），将静态库和其头文件包含在其中，可以在Xcode中方便的使用它。

在OS X上，可能会创建一个[动态连接](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/FrameworkBinding.html)的framework。通过动态连接，framework可以热更新，不需要重新连接，并且在运行时大部分代码是共享出来，大大减少了内存消耗，提高了系统的性能。

在iOS8之后才准许开发者使用第三方动态库，这里我们不会讲动态库，[这里有很好的说明](http://foggry.com/blog/2014/06/12/wwdc2014zhi-iosshi-yong-dong-tai-ku/)。这里我们还是从如何制作静态的Framwork来讲起。

## 创建Framework
我们以FrameworkDemo的项目为例。

#### Step 1 创建一个空的视图工程。
![创建工程](./zhi_zuo_framework/01.png)

我们不是创建静态库嘛，为什么是一个视图工程？向下看吧。

### Step 2 创建静态Framework
#### Step 2.1 添加一个新的target
![添加target](./zhi_zuo_framework/02.png)
#### step 2.2 以工程名添加bundle

![添加bundle](./zhi_zuo_framework/03.png)
![bundel更改成framework扩展](./zhi_zuo_framework/04.png)
正如前面所说framework可以理解为一个资源的结合，而bundle正好满足这方面的要求。这里需要将bundle Extension更改成framework。

**注意：再创建bundle之前我们需要先将之前创建的FrameworkDemo的target更改下名字，为了避免与framework名字冲突，这里更改成FrameworkDemoTest。**
这样我们就有了如下的4个target。
![4个targets](./zhi_zuo_framework/05.png)
前面三个targets用于做例子和单元测试。最后的FrameworkDemo就是我们需要的静态framework。但是现在还不能够使用。

#### Step 2.3 修改Bundle的属性
如上图在target中选择FrameworkDemo。
#### Step 2.4 FrameworkDemo:选择Build Settings->->  Architectures -> Base SDK  改为Latest iOS(iOS9.1)



## 如何使用Framework

## 如何在Framework中使用第三方库

## 多架构编译

## 打包资源