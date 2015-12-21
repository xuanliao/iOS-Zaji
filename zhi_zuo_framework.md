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
前面三个targets用于Demo和单元测试。最后的FrameworkDemo就是我们需要的静态framework。但是现在还不能够使用。

#### Step 2.3 修改Bundle的属性
如上图在target中选择FrameworkDemo。
#### Step 2.4 FrameworkDemo:选择Build Settings->->  Architectures -> Base SDK  改为Latest iOS(iOS9.1)
![设置BaseSDK](./zhi_zuo_framework/06.png)
#### Step 2.5 FrameworkDemo:在Deployment下，
* 确保"OS X Deployment Target"指向最新的os x
将“Targeted Device。
* Family”更改成“1、2”，意思表明是iPhone/iPad。
* 将"iOS Deployment Traget"，更改成"iOS 7.0", 表明静态库支持7.0以上版本。

![设置Deployment1](./zhi_zuo_framework/07.png)
![设置Deployment2](./zhi_zuo_framework/08.png)

#### Step 2.6 因为创建的framework是供其他人使用，最好禁掉以下的功能（无效代码和Debug符号），让用户自己选择对自己项目有利的部分。
* Dead Code Stripping设置为NO
* Strip Debug Symbol During Copy 设置为NO
* Strip Style设置为Non-Global Symbols
* Mac-O Type设置为Relocatable Object File，选择生成文件格式
![设置Deployment2](./zhi_zuo_framework/09.png)

#### Step 2.7 如果在[Step 2.2](./zhi_zuo_framework.md#step-22-以工程名添加bundle)中没有设置Bundle Extension为framework，可以在Packaing中，将"Wrapper Extention"改为"framework"。
![设置Wrapper Extention](./zhi_zuo_framework/10.png)
#### Step 2.8 在 info标签中，将“Bundle OS Type Code” 改为 “FMWK”（Framework ）
![设置info](./zhi_zuo_framework/11.png)
#### Step 2.9 在Build Phases标签，加入Copy Headers，用于公开发布头文件。
![Copy Headers](./zhi_zuo_framework/12.png)
添加完成后会出现Public、Private和Project。
![Headers](./zhi_zuo_framework/13.png)
**注意：**这三个组中Public是你期望公布出去的头文件，Private下的头文件依然是可以暴露出来的，而Project下的头文件对于你的工程来说才是“私有”的，因此应该尽量将头文件加入Public或者Project下面。

#### Step 3 管理项目Target
![Manage Schemes](./zhi_zuo_framework/14.png)
![Manage Schemes管理界面](./zhi_zuo_framework/15.png)
如上图FrameworkDemoTest是测试app，FrameworkDemo是我们需要生成静态Framework。
![Products](./zhi_zuo_framework/16.png)

在Products下面就是我们run后生成的文件，现在红色表明没有生成。

接下来我们来给我们的framework添加共享代码，这边添加Test类为例。
* 创建Test类
![创建Test类1](./zhi_zuo_framework/17.png)
![创建Test类2](./zhi_zuo_framework/18.png)
**注意：**targets选择的是FrameworkDemo。

* 公布Test头文件
 
我们不仅可以在[build-phases](./zhi_zuo_framework.md#step-29-在build-phases标签加入copy-headers用于公开发布头文件)中选择头文件，还可以在辅助面板中直接选择公布的属性。
![公布头文件](./zhi_zuo_framework/19.png)

* 添加代码

Test.h
```
@interface Test : NSObject

+ (void)testSuccess;

@end

```
Test.m
```
@implementation Test

+ (void)testSuccess {
    NSLog(@"Hello world, FrameworkDemo");
}

@end

```
到此，前期的准备工作已经完成，我们可以选择我们需要编译的版本，command+R运行获得Product。
![选择版本](./zhi_zuo_framework/21.png)
![Framework结构](./zhi_zuo_framework/20.png)
如图我们可以清楚的看到生成的framework的目录下包含FrameworkDemo静态库（.a）和Headers公布的头文件目录。

## 如何使用Framework
在FrameworkDemoTest.app中我们可以编写示例代码，如图，我们在ViewController中使用了刚刚编写的Test类。
![示例代码](./zhi_zuo_framework/22.png)
但是很不幸的是运行，报错了。
```
Undefined symbols for architecture i386:
  "_OBJC_CLASS_$_Test", referenced from:
      objc-class-ref in ViewController.o
ld: symbol(s) not found for architecture i386
clang: error: linker command failed with exit code 1 (use -v to see invocation)

```
原因是因为我们没有将生成的Framework添加到Demo项目中。

很简单，将之前我们生成的FrameworkDemo.framework直接拖到项目中。
![添加Framework](./zhi_zuo_framework/23.png)

再次运行我们可以看到控制台中输出Hello world, FrameworkDemo。 

![引用成功](./zhi_zuo_framework/24.png)

**我们成功啦！**

## 如何在Framework中使用第三方库（使用Cocoapods）
编写项目库，正如开发项目一样，难免会应用第三方代码。而我们如何来引入第三方代码呢？
* 直接导入第三方代码，这种方式很不美观，我们不仅得当成自己的代码维护，而且还的管理它们的更新。
* [Cocoapods](https://cocoapods.org/)给我们带来另一种，把我们从第三方代码的管理中解脱出来，事实上这个也是现有iOS开发中使用最多的项目依赖管理方式。

问题来了我们如何在编写静态Framework时使用Cocoapods呢？那么跟着教程来吧。

## 多架构编译

## 打包资源