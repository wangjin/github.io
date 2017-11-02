---
title: Android-onLayout与layout详解
date: 2017-07-06 11:11:35
tags:
 - Android
 - 安卓
---

# onLayout
```Java
protected abstract void onLayout(boolean changed,int left, int top, int right, int bottom);
```
onLayout方法是ViewGroup中子View的布局方法，用于放置子View的位置。放置子View很简单，只需在重写onLayout方法，然后获取子View的实例，调用子View的layout方法实现布局。在实际开发中，一般要配合onMeasure测量方法一起使用。
onLayout在ViewGroup中定义是抽象方法，继承该类必须实现onLayout方法，而ViewGroup的onMeasure并非必须重写的。View的放置都是根据一个矩形空间放置的，onLayout传下来的l,t,r,b分别是放置父控件的矩形可用空间（除去margin和padding的空间）的左上角的left、top以及右下角right、bottom值。

# layout
layout方法是View的放置方法，在View类实现。调用该方法需要传入放置View的矩形空间左上角left、top值和右下角right、bottom值。这四个值是相对于父控件而言的。例如传入的是（10, 10, 100, 100），则该View在距离父控件的左上角位置(10, 10)处显示，显示的大小是宽高是90(参数r,b是相对左上角的)，这有点像绝对布局。
```Java
public void layout(int left, int top, int right, int bottom);
```

开发所用到RelativeLayout、LinearLayout、FrameLayout...这些都是继承ViewGroup的布局。这些布局的实现都是通过都实现ViewGroup的onLayout方法，只是实现方法不一样。