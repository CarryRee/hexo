---
title: 将Framework提交Cocoapods
date: 2018-02-09 14:13:43
tags:
---


## 一.上传framework到Github
参考[打包framework上传到github](http://carryree.com/2018/02/08/uploadGithub/)


## 二.安装cocoapods的打包插件

1.安装

```
sudo gem install cocoapods-packager
```

2.注册

```
pod trunk register xxxx@xx.com xxxx --verbose
```

xxxx@xx.com 为注册邮箱     
xxxx 为用户名

注册后收到一封邮件，复制邮件打开链接完成注册
<img src="/img/2018:02:09_110201.jpg" width="400">

3.查看注册信息

```
pod trunk me
```

## 三.上传cocoa pods

1.cd 到你的git本地目录

2.在工程根目录初始化一个Podspec文件 最好与你项目同名

```
pod spec create EMTest
```
<img src="/img/2018:02:09_110302.jpg" width="500">

3.配置自己的podspec文件(仅供参考)

```
#
#  Be sure to run `pod spec lint EMTest.podspec' to ensure this is a
#  valid spec and to remove all comments including this before submitting the spec.
#
#  To learn more about Podspec attributes see http://docs.cocoapods.org/specification.html
#  To see working Podspecs in the CocoaPods repo see https://github.com/CocoaPods/Specs/
#

Pod::Spec.new do |s|

  # ―――  Spec Metadata  ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  These will help people to find your library, and whilst it
  #  can feel like a chore to fill in it's definitely to your advantage. The
  #  summary should be tweet-length, and the description more in depth.
  #

  s.name         = "EMTest"
  s.version      = "1.0.0"
  s.summary      = "This is just test."

  # This description is used to generate tags and improve search results.
  #   * Think: What does it do? Why did you write it? What is the focus?
  #   * Try to keep it short, snappy and to the point.
  #   * Write the description between the DESC delimiters below.
  #   * Finally, don't worry about the indent, CocoaPods strips it!
  s.description  = 'test for github, CarryRee'

  s.homepage     = "http://www.emeet.ai"
  # s.screenshots  = "www.example.com/screenshots_1.gif", "www.example.com/screenshots_2.gif"


  # ―――  Spec License  ――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  Licensing your code is important. See http://choosealicense.com for more info.
  #  CocoaPods will detect a license file if there is a named LICENSE*
  #  Popular ones are 'MIT', 'BSD' and 'Apache License, Version 2.0'.
  #

  s.license      = { :type => "MIT", :file => "LICENSE" }


  # ――― Author Metadata  ――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  Specify the authors of the library, with email addresses. Email addresses
  #  of the authors are extracted from the SCM log. E.g. $ git log. CocoaPods also
  #  accepts just a name if you'd rather not provide an email address.
  #
  #  Specify a social_media_url where others can refer to, for example a twitter
  #  profile URL.
  #

  s.author             = { "CarryRee" => "63755364@qq.com" }
  # Or just: s.author    = "CarryRee"
  # s.authors            = { "CarryRee" => "63755364@qq.com" }
  # s.social_media_url   = "http://twitter.com/CarryRee"

  # ――― Platform Specifics ――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  If this Pod runs only on iOS or OS X, then specify the platform and
  #  the deployment target. You can optionally include the target after the platform.
  #

  s.platform     = :ios
  s.platform     = :ios, "9.0"

  #  When using multiple platforms
  # s.ios.deployment_target = "5.0"
  # s.osx.deployment_target = "10.7"
  # s.watchos.deployment_target = "2.0"
  # s.tvos.deployment_target = "9.0"


  # ――― Source Location ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  Specify the location from where the source should be retrieved.
  #  Supports git, hg, bzr, svn and HTTP.
  #

  s.source       = { :git => "https://github.com/CarryRee/EMTest.git", :tag => s.version.to_s  }


  # ――― Source Code ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  CocoaPods is smart about how it includes source code. For source files
  #  giving a folder will include any swift, h, m, mm, c & cpp files.
  #  For header files it will include any header in the folder.
  #  Not including the public_header_files will make all headers public.
  #

  #s.source_files  = "Classes", "Classes/**/*.{h,m}"
  #s.exclude_files = "Classes/Exclude"
  s.vendored_frameworks = 'EMTest.framework'

  # s.public_header_files = "Classes/**/*.h"


  # ――― Resources ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  A list of resources included with the Pod. These are copied into the
  #  target bundle with a build phase script. Anything else will be cleaned.
  #  You can preserve files from being cleaned, please don't preserve
  #  non-essential files like tests, examples and documentation.
  #

  # s.resource  = "icon.png"
  # s.resources = "Resources/*.png"

  # s.preserve_paths = "FilesToSave", "MoreFilesToSave"


  # ――― Project Linking ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  Link your library with frameworks, or libraries. Libraries do not include
  #  the lib prefix of their name.
  #

  # s.framework  = "SomeFramework"
  # s.frameworks = "SomeFramework", "AnotherFramework"

  # s.library   = "iconv"
  # s.libraries = "iconv", "xml2"


  # ――― Project Settings ――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  If your library depends on compiler flags you can set them in the xcconfig hash
  #  where they will only apply to your library. If you depend on other Podspecs
  #  you can include multiple dependencies to ensure it works.

  s.requires_arc = true

  # s.xcconfig = { "HEADER_SEARCH_PATHS" => "$(SDKROOT)/usr/include/libxml2" }
  # s.dependency "JSONKit", "~> 1.4"

end

```

3.验证

```
pod lib lint
```

<img src="/img/2018:02:09_112103.jpg" width="300">

4.提交cocoa pods (这个有点耐心, 需要会时间)

```
pod trunk push EMTest.podspec
```
<img src="/img/2018:02:09_113004.jpg" width="500">

注意记得把.podspec也上传到Github中

```
git add EMTest.podspec
git commit -m "pod"
git push -u origin master
```


5.成功后搜索

```
pod search EMTest 
```

6.如果搜索不出来 更新下cocoapods

```
pod setup  
```
7.如果还是没有，一直显示

>[!] Unable to find a pod with name, author, summary, or description matching `EMTest`

执行

```
rm ~/Library/Caches/CocoaPods/search_index.json
```

在执行pod search就会重新搜索
<img src="/img/2018:02:09_141205.jpg" width="500">

