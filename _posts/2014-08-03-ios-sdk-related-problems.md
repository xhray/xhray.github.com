---
layout: post
title: "iOS SDK相关问题"
description: ""
category: iOS
tags: [loadView, viewDidLoad, viewWillAppear, viewDidAppear]
---
{% include JB/setup %}

## 1. loadView和viewDidLoad的不同之处?

>
不要在loadView里读取self.view。只设置，不读取！
>
读取self.view的时，若view没有被加载，get方法会调用loadView方法。若在loadView方法里读取self.view，会造成循环调用。

>
通常情况下，会在loadView中构建试图，如：

	UIView *view = [UIView alloc] init...];
	...
	[view addSubview:whatever];
	[view addSubView:whatever2];
	...
	self.view = view;
	[view release];

>
loadView是UIViewController中加载view并将其赋给属性“view”的方法。当从UIViewController派生子类时，可以重写loadView方法设置view属性。

>
当view加载完成时，viewDidLoad会被调用。viewDidLoad在loadView之后被调用，在这里可以进一步控制view的设置。
	
reference: [iPhone SDK: what is the difference between loadView and viewDidLoad?](http://stackoverflow.com/a/574291)


## 2. 在哪里add subviews: ViewDidAppear? ViewDidLoad? ViewWillAppear?

* viewDidLoad - 当需要控制子view跟随view一同呈现时，需要将添加子view这个动作放到ViewDidLoad方法。该方法在view加载到内存时被调用。比如：假设view是一个包含3个label的form，那么就将增加label到view上这个动作放到ViewDidLoad。
* viewWillAppear － 通常用来更新数据。继续上面的例子，通过在这个方法中加载服务器数据到form上。创建UIView属于较为消耗大的操作，应该尽量避免在ViewWillAppepear进行这个操作。当这个方法被调用时，意味着设备已经准备好将UIView呈现给用户，在这里进行任何消耗大的操作会影响性能，并且用户能切实感受到（比如动画延时）。
* viewDidAppear － 在这个方法中，开启新的线程去执行那些执行时间长的事情。比如调用webservice为form获取额外的数据。幸好的是view已经准备好并呈现给用户，因此，在加载数据时，你可以向用户提示友好的“Waiting”信息。

reference: [viewDidLoad, viewWillAppear, viewDidAppear](http://stackoverflow.com/a/2281560)


## 3. 何时重写loadView

* 当在view controller及其派生类中使用自定义root view时，就应该在loadView方法中创建view，并将其设置为view controller的view属性（注意不要调用super）。
* 在viewDidLoad方法中修改view controller's view或为其添加subview
* 推荐的做法是在viewDidLoad中创建UILabel或UIWebView，并在viewDidUnload将它们释放，并将它们的引用变量设为nil。

reference: [loadView](http://stackoverflow.com/a/2280642)

## 4. [ARC模式下，是否需要在dealloc方法中设置属性为nil](http://stackoverflow.com/q/7906804)




