---
title: framework
date: 2018-01-20 16:08:57
tags:
---

## 工程创建
1.打开Xcode -> Create a new Xcode project,然后选择如图的Cocoa Touch Framework, 选择语言、填好包名，组织名
![outputScreen](/img/2018:01:19_164701.jpg)

2.工程的Targets下的配置General -> Deployment Info下的Deplayment Target 设置使用的最低版本，我们就设置成9.0便于说明

3.工程的Targets下的配置Build Settings -> Linking下的Mach-O type 设置为Static Library
![outputScreen](/img/2018:01:20_101602.png)

## 代码示例
1.新建三个类test1、test2、test3
代码基本如下：

```
@interface Test1 : NSObject

- (void)print;

@end
```
test2、test3同理

2.然后设置test1.h和test1.m完全对外公开、Test2.h公开，test2.m隐蔽、test3.h和test3.m不对外公开

3.工程的Targets下的配置Build Phases -> Headers下的文件根据上面所示，如下设置，
注意：新建一个类的时候，默认Project会默认对应该类的头文件，如果需要完全公开，.m也要对外的话，点击下面的“+”,添加.m，然后将其拖到Public之中,要对外的都放入Public，不对外的放入Project；
![outputScreen](/img/2018:01:20_153303.png)

## 生成真机和模拟器合并framework
问题：framework如果选择真机运行Products会生成真机framework，只能在真机运行，不能再模拟器运行，同理如果选择模拟器运行Products会生成模拟器framework，只能在模拟器运行，不能在真机运行；

为了解决以上问题，我们可以通过脚本生成生成真机和模拟器合并framework

1.Xcode -> File -> Target -> Cross-platform 下面的 other 点击 Aggregate 然后输入了脚本名EMAggregate
![outputScreen](/img/2018:01:20_154804.png)

2.工程的Targets下EMAggregate 的 Build Phases 点击左上角“+”，选择New Run Script Phase;
![outputScreen](/img/2018:01:20_155205.png)

3.添加如下代码：

```
# Sets the target folders and the final framework product.

# 如果工程名称和Framework的Target名称不一样的话，要自定义FMKNAME

# 例如: FMK_NAME = "MyFramework"

FMK_NAME=${PROJECT_NAME}

# Install dir will be the final output to the framework.

# The following line create it in the root folder of the current project.

INSTALL_DIR=${SRCROOT}/Products/${FMK_NAME}.framework

# Working dir will be deleted after the framework creation.

WRK_DIR=build

DEVICE_DIR=${WRK_DIR}/Release-iphoneos/${FMK_NAME}.framework

SIMULATOR_DIR=${WRK_DIR}/Release-iphonesimulator/${FMK_NAME}.framework

# -configuration ${CONFIGURATION}

# Clean and Building both architectures.

xcodebuild -configuration "Release" -target "${FMK_NAME}" -sdk iphoneos clean build

xcodebuild -configuration "Release" -target "${FMK_NAME}" -sdk iphonesimulator clean build

# Cleaning the oldest.

if [ -d "${INSTALL_DIR}" ]

then

rm -rf "${INSTALL_DIR}"

fi

mkdir -p "${INSTALL_DIR}"

cp -R "${DEVICE_DIR}/" "${INSTALL_DIR}/"

# Uses the Lipo Tool to merge both binary files (i386 + armv6/armv7) into one Universal final product.

lipo -create "${DEVICE_DIR}/${FMK_NAME}" "${SIMULATOR_DIR}/${FMK_NAME}" -output "${INSTALL_DIR}/${FMK_NAME}"

rm -r "${WRK_DIR}"

open "${INSTALL_DIR}"

```

4.运行EMAggregate，然后就会在该工程旁边生成目录Products，里面就是合并的
![outputScreen](/img/2018:01:20_160506.png)
