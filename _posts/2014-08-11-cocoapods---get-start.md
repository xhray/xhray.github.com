---
layout: post
title: "CocoaPods - 起步"
description: ""
category: iOS
tags: [iOS, Xcode, CocoaPods, pod repo, Podfile, podspec, 依赖项管理]
---
{% include JB/setup %}

## 安装CocoaPods

### 安装

CocoaPods运行在Ruby，mac一般都会装有。所以，你只需要做的是更新RubyGems。

	sudo gem update --system
	
执行命令后，会在终端看到类似下面的输出：

![TerminalOutput](http://cdn2.raywenderlich.com/wp-content/uploads/2014/03/TerminalOutput.png)	

执行下面的命令安装CocoaPods:

	sudo gem install cocoapods
	
安装过程中终端可能会弹出一下需要你确认的信息：

	rake's executable "rake" conflicts with /usr/bin/rake
	Overwrite the executable? [yN]
	
如果是，直接输入y确认。

最后，输入以下命令完成CocoaPods安装(这个命令将[CocoaPods Specs repository](https://github.com/CocoaPods/Specs) clone到本机的~/.cocoapods/目录下)：

	pod setup

另外，ruby的软件源rubygems.org被墙，因此需要更新以下ruby的源：

	gem sources --remove https://rubygems.org/
	gem sources -a http://ruby.taobao.org/
	gem sources -l
	
### 使用CocoaPods镜像索引

所有的项目的Podspec文件都托管在https://github.com/CocoaPods/Specs。第一次执行pod setup时，CocoaPods会将这些podspec索引文件更新到本地的 ~/.cocoapods/目录下，这个索引文件比较大，所以第一次更新时非常慢。

可以将CocoaPods设置成使用国内的CocoaPods索引库镜像：

	pod repo remove master
	pod repo add master [https://gitcafe.com/akuandev/Specs.git | http://git.oschina.net/akuandev/Specs.git]
	pod repo update
	
---
	
## CocoaPods使用

在终端中进入项目所在目录，然后执行以下命令为项目创建默认的Podfile。你需要在Podfile中声明本项目的依赖项。

	pod init	

执行以下命令，可以使用Xcode打开Podfile进行编辑：

	open -a Xcode Podfile
	
***不要使用TextEdit进行编辑，TextEdit会替换标准的引号，导致CocoaPods出错***	
		
初始时，Podfile的内容如下：

	# Uncomment this line to define a global platform for your project
	# platform :ios, "6.0"
 
	target "ShowTracker" do
 
	end

将***# platform :ios, "6.0"***修改为：***platform :ios, "7.0"***

这个语句告诉CocoaPods，本项目基于iOS7.

接着，向Podfile增加依赖项目：

	pod 'AFNetworking', '2.2.1'
	
保存并退出编辑。

当我们通过CocoaPods引入依赖库时，需要显式或隐式注明引用的依赖库版本，具体写法和含义如下：

	pod 'AFNetworking'              //不注明版本，每次都获取最新版本
	pod 'AFNetworking', '2.0'       //只使用2.0版本
	pod 'AFNetworking', '> 2.0'     //使用高于2.0的版本
	pod 'AFNetworking', '>= 2.0'    //使用不低于2.0的版本
	pod 'AFNetworking', '< 2.0'	//使用小于2.0的版本
	pod 'AFNetworking'. '<= 2.0'    //使用不高于2.0的版本
	pod 'AFNetworking', '~> 0.1.2'  //使用大于等于0.1.2，小于0.2的版本
	pod 'AFNetworking', '~> 0.1'    //使用大于等于0.1，小于1.0的版本
	pod 'AFNetworking', '~> 1.0.0'  //使用大于等于1.0.0，小于2.0.0的版本
	pod 'AFNetworking', '~> 0'      //使用高于0的版本，写这个和什么都不写一样，都表示使用最新版本

执行安装依赖项目的命令：

	pod install
	

安装成功后，使用***[project].xcworkspace***打开项目

---

## 管理私有库

基于我们的一些代码不是开源的，或不放到Github的情况，这时就有需要搭建私有repo。

### 概念解析

* podspec：一个pod的配置是什么，pod的代码放在那里
* Podfile：项目依赖哪个pod，以何种方式依赖，它的podspec放在哪里


### 操作步骤

首先，为需要管理的库创建新的git repository，并打上Tag，如v0.0.1

然后，新建一个用来配置作者等信息的[project].podspec文件:

	Pod::Spec.new do |s|
    	s.name = '[ProjectName]'
    	s.version = '0.0.1'
    	s.license = 'MIT'
    	s.summary = '[summary]'
    	s.homepage = 'http://www.xxx.com'
    	s.description = '[s.description]'
    	s.author = {'author_name' => 'email' }
    	s.source = { :git => 'https://github.com/[username]/[repository].git', :tag => '0.0.1' }
    	s.platform = :ios,'5.0'
    	s.source_files = 'src'
    	s.frameworks = 'SomeFramework','AnotherFramework'
	end

s.source指定代码库地址， s.source_files指定所需文件的所在的文件夹，s.frameworks指定需要用到的framework。

可以使用以下命令初始化一个podspec文件：

	pod spec create PODSPEC_NAME

接着，编辑开发项目中的Podfile，添加：

	pod '[project]', :local => '~/[path]/[project].podspec'
	
或将podspec放在gist上：

	pod '[project]', :podspec => 'https://xxx.github.com/gist/....'	 	

最后，使用：

	pod update

需要注意：

* s.name需要和本podspec文件的名称一致，区分大小写
* 可以在git上打tag，在podspec文件上指定:tag。这样可以保证repo对外保持稳定，不受开发状态影响。
* 可以根据功能/模块对podspec进行分块(subspec)，这样其他人可以选择安装lib的一个/多个子集。在Podfile中，`pod 'CommonLib'`安装整个CommonLib。如只需要其中的UI模块，可以使用 `pod 'CommonLib/UI'`。

一个完整的podspec例子：

	Pod::Spec.new do |s|
		s.name         = "CommonLib"
		s.version      = "0.0.1"
		s.summary      = "common lib for ios team."
		
		s.description  = <<-DESC
                          commonlib for ios development, includes UI components, UIHelper, NetworkHelper, etc.
                         DESC
                   
		s.homepage     = "http://[host]/ios-project-libs/commonlib"
        
		s.license      = "MIT"
		s.authors      = { "author_name" => "email" }
		s.platform     = :ios, '6.0'
		s.source       = { :git => "http://[host]/ios-project-libs/commonlib.git", :tag => "v0.0.1" }
        
		s.resources = "CommonLib/Resource/Localization", "CommonLib/Resource/Images"

		s.dependency "AFNetworking", "~> 2.3.1"
		s.dependency "AFNetworking-MUResponseSerializer", "0.9.9"
		s.dependency "Masonry", "~> 0.5.2"

		s.requires_arc = true

		s.prefix_header_contents = <<-EOS
                              #ifdef __OBJC__
                                #import <Foundation/Foundation.h>

                                #define MAS_SHORTHAND
                                #import "Masonry.h"
 				 
                                #import "AFNetworking.h"
                                #import "MUResponseSerializer.h"
			 
                              #endif

                            EOS

		s.subspec "Models" do |ss|

    		ss.source_files = "CommonLib/Models", "CommonLib/Models/**/*.{h,m}"

		end
  
		s.subspec "Utils" do |ss|

    		ss.dependency "CommonLib/Models"
    		ss.source_files = "CommonLib/Common", "CommonLib/Common/**/*.{h,m}"

		end

		s.subspec "Library" do |ss|

    		ss.source_files = "CommonLib/Library", "CommonLib/Library/**/*.{h,m}"

		end

		s.subspec "UI" do |ss|

    		ss.dependency "CommonLib/Utils"
    		ss.dependency "CommonLib/Library"
    
    		ss.prefix_header_contents = <<-EOS
                                 		  #import "GMDebugHeader.h"
                                		 EOS

    		ss.exclude_files = "CommonLib/Views/UITest.{h,m}"

    		ss.source_files = "CommonLib/Views", "CommonLib/Views/**/*.{h,m}"

		end

	end


修改podspec文件后，可以执行以下命令校验：

	pod spec lint


在搭建私有公共库时（可以在podfile中，以这样的形式使用：`pod 'CommonLib', '~> 0.0.1'`），还需要另外搭建私有的repo，并将项目的podspec文件上传到repo。

私有repo搭建完成后，客户端需要在本机配置相应的repo（~/.cocoapods/）：

	pod repo add REPO_NAME SOURCE_URL	
	
添加Podspec到repo（若已经添加，则这步可以忽略）：

	pod repo push REPO_NAME SPEC_NAME.podspec	

每次执行 `pod install --no-repo-update`或 `pod update --no-repo-update`前，需要更新一下本地的repo（当私有repo有更新时）：

	pod repo update REPO_NAME 	
	


---

reference:

* [CocoaPods Guides](http://guides.cocoapods.org)

* [Introduction to CocoaPods Tutorial](http://www.raywenderlich.com/64546/introduction-to-cocoapods-2)

* [使用CocoaPods管理私有库](http://www.itiger.me/?p=74)

* [AFNetworking podspec](https://github.com/AFNetworking/AFNetworking/blob/master/AFNetworking.podspec)	

* [CocoaPods安装使用及配置私有库](http://www.exiatian.com/cocoapods安装使用及配置私有库/)	

* [CocoaPods进阶：本地包管理](http://www.iwangke.me/2013/04/18/advanced-cocoapods/)

* [用CocoaPods做iOS程序的依赖管理](http://blog.devtang.com/blog/2014/05/25/use-cocoapod-to-manage-ios-lib-dependency/)
	