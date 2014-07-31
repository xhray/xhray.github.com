---
layout: post
title: "UIPickerView"
description: ""
category: iOS
tags: []
---
{% include JB/setup %}


# UIPickerView

#### 调整UIPickerView高度

UIPickerView只允许3种高度(162.0, 180.0, 216.0)

**UIDatePicker也只允许这3种高度**

>
在程序中允许设置UIPickerView的frame，但frame的height小于162，或大于216时，系统会在控制台上输出：
>
Invalid height value 300.0 pinned to 216.0.
>
Invalid height value 66.0 pinned to 162.0.


参考资料：

* [How to change UIPickerView height](http://stackoverflow.com/questions/573979/how-to-change-uipickerview-height)


