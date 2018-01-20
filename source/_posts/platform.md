---
title: 工程的动态库同时支持真机和模拟器
date: 2017-11-22 10:31:25
tags: Framework
categories: 
---


## 问题
最近在工程使用到了第三方提供的SDK，有的第三方提供的是framework的是动态库，只提供真机和模拟器，如果的想同时支持真机和模拟，一想到就是合并，输出一个通用framework

```
lipo -create 【模拟器打包path】 【真机打包path】 -output 【导出兼容版本path】
```

可惜一般得出结果是
![outputScreen](/img/2017:11:22_094601.jpeg)
**分析**：这个命令是静态库使用，动态库并不适用

## 测试Framework
使用XCode新建一个动态库
![newFrameWork](/img/2017:11:22_094701.jpeg)

在 Build Settings 的 Linking 下设置 Mach-O type 为 Dynamic Library
![settingFrame](/img/2017:11:22_094801.jpeg)

代码也简单加一方法，给外部调用

**Test.h**

```
@interface Test : NSObject
- (void)helloWorld;
@end
```

**Test.m**

```
- (void)helloWorld {
NSLog(@"%@", @"Hello, world!");
}
```

通过xcode编译出真机和模拟器的framework
这里我把真机的framework文件夹名字命名为iphoneos、模拟器的framework文件夹名字命名为iphonesimulator，注意：这一步很重要；


## 测试工程
随便新建一个工程，把两个framework拖入工程，然后删除**LinkFrame works and Librarues**, 然后**Embedded Binaries**再添加两个动态库
<img src="/img/2017:11:22_094901.jpeg" width="300">

设置如下图设置
![settingProject2](/img/2017:11:22_095202.jpeg)

最后导入库
```
#import <TestFrame/Test.h>
```
调用方法

```
Test *test = [[Test alloc] init];
[test helloWorld];
```

编译看看，是不是编译不过，没错，两个库冲突了

## 冲突处理

1.删掉其中一个（>-<），废话中的废话

2.共存处理（下面重点介绍）


## 共存处理

1.在两个framework的文件夹下建立一个新文件夹，命名为$PLATFORM_NAME,随便将一个framework复制进去
![settingProject7](/img/2017:11:22_101345.jpeg)

2.将两个framework从工程中删除（逻辑删除），随便讲其中一个拖入工程，注意是拖framework，不是文件夹

3.点击拖入的framework，如图所示，配置路径
![settingProject3](/img/2017:11:22_095701.jpeg)

4.然后配置**Build Settings**下的Framework Search Paths设置为$PLATFORM_NAME文件夹的路径（路径为红色不要紧）
![settingProject4](/img/2017:11:22_095801.jpeg)

5.更改选择为$PLATFORM_NAME文件夹下的framework,然后找到改文件夹去改删去该文件夹（你没有看错），是物理删除该文件夹
![settingProject8](/img/2017:11:22_101645.jpeg)

6.**Embedded Binaries**再添加回$PLATFORM_NAME的framework（路径为浅色不要紧）
<img src="/img/2017:11:22_095845.jpeg" width="300">

7.基本到这里就可以
![settingProject6](/img/2017:11:22_095901.jpeg)
