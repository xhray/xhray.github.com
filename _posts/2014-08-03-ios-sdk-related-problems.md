---
layout: post
title: "iOS SDK相关问题"
description: ""
category: iOS
tags: [loadView, viewDidLoad, viewWillAppear, viewDidAppear, Objective-C, class extension, ivars, instance variables, private methods]
---
{% include JB/setup %}

## loadView和viewDidLoad的不同之处?

>
不要在loadView里读取self.view。只设置，不读取！
>
读取self.view的时，若view没有被加载，get方法会调用loadView方法。若在loadView方法里读取self.view，会造成循环调用。

>
通常情况下，会在loadView中构建视图，如：

	UIView *view = [UIView alloc] init...];
	...
	[view addSubview:whatever];
	[view addSubView:whatever2];
	...
	self.view = view;
	[view release];

>
UIViewController在loadView方法中加载view并将其赋给属性“view”的方法。当从UIViewController派生子类时，可以重写loadView方法设置view属性。

>
当view加载完成时，viewDidLoad会被调用。viewDidLoad在loadView之后被调用，在这里可以进一步控制view的设置。
	
reference: [iPhone SDK: what is the difference between loadView and viewDidLoad?](http://stackoverflow.com/a/574291)


## iOS6，viewDidUnload、viewWillUnload过期

苹果官方文档提到：

>
这个方法在controller的view被释放时调用。在iOS6中，viewDidUnload被抛弃。在low-memory的情况下，views不会被清除。因此这个方法不会被调用。(Deprecated in iOS6.0. Views are no longer purged under low-memory conditions and so this method is never called.)

从iOS6开始，没有必要controller对view或其它对象的引用。

***可以在ViewController的didReceiveMemoryWarning方法中设置view或其它组件为nil。可以覆盖该方法来释放被view controller使用的任何多余的内存。但app中不应该调用这个方法，当系统检测到内存紧张时，会自动调用该方法。***

***在重写didReceiveMemoryWarning时，最好的做法时不要释放任何view对象，只释放那些不再需要的个人数据(custom data)。至于view对象，就让view controller的父类来处理。***


## 在哪里add subviews: ViewDidAppear? ViewDidLoad? ViewWillAppear?

* viewDidLoad - 当需要控制子view跟随view一同呈现时，需要将添加子view这个动作放到ViewDidLoad方法。该方法在view加载到内存时被调用。比如：假设view是一个包含3个label的form，那么就将增加label到view上这个动作放到ViewDidLoad。
* viewWillAppear － 通常用来更新数据。继续上面的例子，通过在这个方法中加载服务器数据到form上。创建UIView属于较为消耗大的操作，应该尽量避免在ViewWillAppepear进行这个操作。当这个方法被调用时，意味着设备已经准备好将UIView呈现给用户，在这里进行任何消耗大的操作会影响性能，并且用户能切实感受到（比如动画延时）。
* viewDidAppear － 在这个方法中，开启新的线程去执行那些执行时间长的事情。比如调用webservice为form获取额外的数据。幸好的是view已经准备好并呈现给用户，因此，在加载数据时，你可以向用户提示友好的“Waiting”信息。

reference: [viewDidLoad, viewWillAppear, viewDidAppear](http://stackoverflow.com/a/2281560)


## 何时重写loadView

* 当在view controller及其派生类中使用自定义root view时，就应该在loadView方法中创建view，并将其设置为view controller的view属性（注意不要调用super）。
* 在viewDidLoad方法中修改view controller's view或为其添加subview
* 推荐的做法是在viewDidLoad中创建UILabel或UIWebView，并在viewDidUnload将它们释放，并将它们的引用变量设为nil。

reference: [loadView](http://stackoverflow.com/a/2280642)


## ARC模式下，是否需要在dealloc方法中设置属性为nil

就算在手动管理内存模式（MRR, manual memory management），也不应该在dealloc中设置property为nil。

在MRR模式下，你应该释放实例变量（ivars）。设置property为nil会调用set方法，而set方法可能包含不应该在dealloc方法执行的代码。也有可能会触发KVO notifications。释放ivar而不是property可以避免产生未预期的结果。

在ARC模式下，系统会自动释放ivars。如果所做的只是这些，你甚至不需要实现dealloc方法。但如果代码包含分配缓冲区（buffers）等非对象（non-objects）ivars，你还是需要在dealloc方法中处理它们。

进一步说，如果将自身设为其它对象的delegate，那么在dealloc方法中，你应该解除这种关系（代码如下：[obj setDelegate = nil]），但delegate属性为weak的情况除外。


reference:

[Do I set properties to nil in dealloc when using ARC](http://stackoverflow.com/a/7906891)


## Objective-C class extension

Q: 在class extension中定义私有方法和完全不声明私有方法之间的差异？

	@interface Class()
	
	- (void)bar;
	
	@end
	
	
	@implementation Class
	
	- (void)foo
	{
		[self bar];
	}
	
	- (void)bar
	{
		NSLog(@"bar")
	}
	
	@end
	
和

	@implementaion Class
	
	- (void)foo
	{
		[self bar];
	}
	
	- (void)bar
	{
		NSLog(@"bar");
	}
	
	@end

A: 现在不需要声明私有方法。在不久前，私有方法需要预先声明，大多数人选择在class extension中声明。从Xcode 4.4开始，编译器已经有能力检测implementation中的私有方法，从而不需要在别处预先声明。

Q: 在class extension中声明变量和implementation中的差异？

	@interface Class()
	{
		NSArray *mySortedArray;
	}
	
	@end
	
	@implementation Class
	
	@end
	
和

	@implementation Class
	
	NSArray *mySortedArray;
	
	@end	
	
A: 在class extension中声明的变量是类的实例变量，而在implementation中直接声明的变量是全局变量（遵循c的全局变量语义）！

reference：

[Objective-C class extension](http://stackoverflow.com/a/14675389)	


