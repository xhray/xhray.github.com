---
layout: post
title: "iOS：使用UIEdgeInsetsMake制作可伸缩按钮"
description: ""
category: iOS
tags: [iOS, stretchable button, UIEdgeInsetsMake]
---
{% include JB/setup %}


在一个iOS app里，通常会使用很多的button。而这些button有同样的背景色（backgroud）但在不同的屏幕上有不同的大小（即使在iOS7上，button没有背景）。举个例子，Save button会比Submit button短，但它们都以紫色为背景色。

在此情况下，你通常不想为button定义各种不同大小背景图片。幸运地，使用**UIEdgeInsetsMake**方法可以非常容易地令一张这样的图片：

![origin image](http://natashatherobot.com/wp-content/uploads/purple_button@2x.png)

变成这样的：

![target image](http://natashatherobot.com/wp-content/uploads/Screen-Shot-2013-10-24-at-6.32.55-AM.png)

这个想法非常简单。原图的方形圆角按钮的边缘需要保留，但中间部分可以在横向/纵向两个方向进行复制，直到达到需要的大小。

使用**UIEdgeInsets**，并指定在拉伸图片时，top、left、bottom、right忽略的像素数可以达到此目的。**UIEdgeInsets**是一个包含4个float类型字段（top、left、bottom、right）的结构体（struct）。

	typedef struct {
		CGFload top, left, bottom, right;
	} UIEdgeInsets;

## 复制一切

现在我新建一个项目，在storyboard上添加一个自定义button，然后在ViewController上为那个button创建outlet。在ViewDidLoad:方法，我调用了私有的configureButton方法。该方法在最开始的时候将UIEdgeInsets初始为**UIEdgeInsetsMake(0, 0, 0, 0)**。

	- (void)configureButton
	{
    	UIEdgeInsets edgeInsets = UIEdgeInsetsMake(0, 0, 0, 0);
    	UIImage *backgroundButtonImage = [[UIImage imageNamed:@"purple_button.png"]
                                      resizableImageWithCapInsets:edgeInsets];
    	[self.purpleButton setBackgroundImage:backgroundButtonImage
                                 forState:UIControlStateNormal];
	}

随着整张图片不断被复制（在UIEdgeInsets没有设置忽略像素），最后生成的图片为：

![Screen-Shot-2013-10-24-at-6.47.11-AM](http://natashatherobot.com/wp-content/uploads/Screen-Shot-2013-10-24-at-6.47.11-AM.png)

## 纵向拉伸

为了让图片纵向拉伸，我们需要忽略左边缘和右边缘，如下图所示：

![purple_button_vertically](http://natashatherobot.com/wp-content/uploads/purple_button_vertically.png)

在这些边缘之间的所有东西可以被复制。为达到这样的效果，我们需要忽略左/右各8各像素，设置UIEdgeInsets为UIedgeInsetsMake(0, 8, 0, 8)：

	- (void)configureButton
	{
    	UIEdgeInsets edgeInsets = UIEdgeInsetsMake(0, 8, 0, 8);
    	UIImage *backgroundButtonImage = [[UIImage imageNamed:@"purple_button.png"]
                                      resizableImageWithCapInsets:edgeInsets];
    	[self.purpleButton setBackgroundImage:backgroundButtonImage
                                 forState:UIControlStateNormal];
	}

现在的效果看起来好多了！

![Screen-Shot-2013-10-24-at-6.53.42-AM](http://natashatherobot.com/wp-content/uploads/Screen-Shot-2013-10-24-at-6.53.42-AM.png)

## 横向拉伸

类似地，如果需要将图片横向拉伸，只需要忽略图片上/下各8像素，将图片的中间部分复制：

![purple_button_horizontal-1](http://natashatherobot.com/wp-content/uploads/purple_button_horizontal-1.png)

所以，如果只是横向拉伸，UIEdgeInsets被设置为UIEdgeInsetsMake(8, 0, 8, 0)，configureButton的方法如下：

	- (void)configureButton
	{
    	UIEdgeInsets edgeInsets = UIEdgeInsetsMake(8, 0, 8, 0);
    	UIImage *backgroundButtonImage = [[UIImage imageNamed:@"purple_button.png"]
                                      resizableImageWithCapInsets:edgeInsets];
    	[self.purpleButton setBackgroundImage:backgroundButtonImage
                                 forState:UIControlStateNormal];
	}

类似上面得例子，图片可以横向拉伸，但纵向没有：

![Screen-Shot-2013-10-24-at-7.03.20-AM](http://natashatherobot.com/wp-content/uploads/Screen-Shot-2013-10-24-at-7.03.20-AM.png)

## 双向拉伸

为了让图片同时在横向和纵向拉伸，我们需要忽略上/下、左/右8像素：

![purple_button_both-1](http://natashatherobot.com/wp-content/uploads/purple_button_both-1.png)

我们只需要简单地设置UIEdgeInsets为UIEdgeInsetsMake(8, 8, 8, 8)，在每个方向上忽略8像素：

	- (void)configureButton
	{
		UIEdgeInsets edgeInsets = UIEdgeInsetsMake(8, 8, 8, 8);
    	UIImage *backgroundButtonImage = [[UIImage imageNamed:@"purple_button.png"]
                                      resizableImageWithCapInsets:edgeInsets];
    	[self.purpleButton setBackgroundImage:backgroundButtonImage
                                 forState:UIControlStateNormal];
	}

Voilà! 我们现在拥有一个非常好看地按钮：

![Screen-Shot-2013-10-24-at-6.32.55-AM](http://natashatherobot.com/wp-content/uploads/Screen-Shot-2013-10-24-at-6.32.55-AM.png)

无论什么时候，只要你需要一个紫色的按钮，你只需要一张小的方图，然后忽略其边角部分并将中间部分不断复制，就可以将原图改变成任意大小。


### 原文链接

[iOS: How To Make A Stretchable Button With UIEdgeInsetsMake](http://natashatherobot.com/ios-stretchable-button-uiedgeinsetsmake)