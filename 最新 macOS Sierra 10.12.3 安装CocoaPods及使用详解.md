最新 macOS Sierra 10.12.3 安装CocoaPods及使用详解

## 一、什么是CocoaPods
每种语言发展到一个阶段，就会出现相应的依赖管理工具，例如 Java 语言的 Maven，nodejs 的 npm。随着 iOS 开发者的增多，业界也出现了为 iOS 程序提供依赖管理的工具，它的名字叫做：CocoaPods。

CocoaPods项目的源码 在 Github 上管理。该项目开始于 2011 年 8 月 12 日，经过多年发展，现在已经成为 iOS 开发事实上的依赖管理标准工具。开发 iOS 项目不可避免地要使用第三方开源库，CocoaPods 的出现使得我们可以节省设置和更新第三方开源库的时间。

## 二、为什么要使用CocoaPods
在使用CocoaPods之前，开发项目需要用到第三方开源库的时候，我们需要

#### 1.把开源库的源代码复制到项目中

#### 2.添加一些依赖框架和动态库

#### 3.设置  -ObjC，-fno-objc-arc 等参数

#### 4.管理他们的更新

在使用CocoaPods后，我们只需要把用到的开源库放到一个名为Podfile的文件中，然后执行  pod install就可以了，Cocoapods就会自动将这些第三方开源库的源码下载下来，并且为我们的工程设置好响应的系统依赖和编译参数。

## 三、CocoaPods的原理
CocoaPods的原理是将所有的依赖库都放到另一个名为Pods的项目中，然后让主项目依赖Pods项目，这样，源码管理工作都从主项目移到了Pods项目中。Pods项目最终会编译成一个名为libPods.a的文件，主项目只需要依赖这个.a文件即可。

## 四、CocoaPods的安装
CocoaPods可以方便地通过Mac自带的RubyGems安装。

打开Terminal（Mac电脑自带的终端），然后按照以下提示操作即可：

####1.设置ruby的软件源

这是因为ruby的软件源rubygems.org因为使用亚马逊的云服务，被我天朝屏蔽了，需要更新一下ruby的源，过程如下：

 	  gem sources -l #(查看当前ruby的源)

	  gem sources --remove https://ruby.taobao.org/ #(移除当前taobao ruby的源)

	  gem sources -a https://gems.ruby-china.org/ #(设置当前ruby的源为我天朝的)

	  gem sources -l #(再次查看当前ruby的源)

如果Terminal输出：

		*** CURRENT SOURCES ***

		https://gems.ruby-china.org/
		

就证明ruby的软件源已经设置OK了。

#### 2.设置gem为最新版本
如果gem太老，可以尝试用如下命令升级gem：

在Terminal输入以下命令：

	sudo gem update --system

升级成功后会提示: Latest version currently installed. Aborting.

#### 3.执行安装CocoaPods命令

注意：OS X 10.11 升级，虽然官方声称只是一个小的升级，但对于开发者而言，cocoapods需要重新安装，但按照以前的安装方式，在Terminal输入以下命令：

	sudo gem install  cocoapods

如果报以下错误：

ERROR:  While executing gem ... (Errno::EPERM)

	Operation not permitted - /usr/bin/xcodeproj

解决方案1：

执行下面的命令并重启

	sudo nvram boot-args="rootless=0"

	sudo reboot

重启之后, 执行这个命令检查

	sudo gem install cocoapods -V

如果依旧有错误，使用第二个方案

解决方案2：

	sudo gem install -n /usr/local/bin cocoapods

	pod setup
	
还有一点需要注意，pod setup在执行时，会输出Setting up CocoaPods master repo，但是会等待比较久的时间。这步其实是 Cocoapods 在将它的信息下载到 ~/.cocoapods目录下，如果你等太久，可以试着 cd 到那个目录，用du -sh *来查看下载进度。

安装成功后,你会看到:Setup completed

## 五、Cocoapods的使用
随便以一种方式新建一个名为Podfile的文件放到你的工程根目录下（不能写成别的名字，也可以自己在工程根目录里面直接新建）；建议用pod构建一个Podfile，如下：
		
	pod init

Podfile文件内容的格式应该如下：

	platform :ios, '8.0' #(注明你的开发平台以及版本，'8.0'忽略不写即为最新版本)
	target 'YourProject' do
	
	pod 'AFNetworking', '~> 2.5.3' #('~> 2.5.3'为版本号，忽略不写即为最新版本)
	pod 'SDWebImage', '~> 3.7.2'
	

然后在Terminal进入工程所在的根目录（工程根目录）中执行 ：

	pod install

这样，AFNetworking和SDWebImage就已经下载完成并且设置好了编译参数和依赖，以后使用的时候切记如下两点：

##### 1.从此以后需要使用Cocoapods生成的 .xcworkspace文件来打开工程，而不是使用以前的.xcodeproj文件
##### 2.每次更改了Podfile文件，都需要重新执行一次 pod update 更新命令:


	pod update

查找第三方库：

你如果不知道 cocoaPods 管理的库中，是否有你想要的库，那么你可以通过 pod search 命令进行查找，以下是我用 pod search json 查找到的所有可用的库：

可以在Terminal中输入：

	pod search AFNetworking

回车之后就可以看到和你搜索的关键字相关的一些库类，如图：


关于 Podfile.lock
当你执行pod install之后，除了 Podfile 外，CocoaPods 还会生成一个名为Podfile.lock的文件，Podfile.lock 应该加入到版本控制里面，不应该把这个文件加入到.gitignore中。因为Podfile.lock会锁定当前各依赖库的版本，之后如果多次执行pod install 不会更改版本，要pod update才会改Podfile.lock了。这样多人协作的时候，可以防止第三方库升级时造成大家各自的第三方库版本不一致



附：如何使用CocoaPods的镜像索引:
所有项目的Podspec文件都托管在https://github.com/CocoaPods/Specs,

第一次执行	pod setup	时,CocoaPods会将这些podspec索引文件更新到本地的~/.cocoapods目录下,

这个索引文件比较大,所以第一次更新时非常慢.

友好人士在国内的服务器建立了Cocoapods索引库的镜像,

所以执行索引跟新操作时候会快很多.具体操作方法如下:

	pod repo remove master

	pod repo add master https://gitcafe.com/akuandev/Specs.git

	pod repo update

这是使用gitcafe上的镜像,将以上代码中的 https://gitcafe.com/akuandev/Specs.git

替换成 http://git.oschina.net/akuandev/Specs.git 即可使用oschina上的镜像。
