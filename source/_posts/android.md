---
title: android
date: 2020-06-03 20:33:08
author: xieziqi
img: /source/images/xxx.jpg
top: true
cover: true
coverImg: /images/1.jpg
password: 8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: false
mathjax: false
summary: 这是你自定义的文章摘要内容，如果这个属性有值，文章卡片摘要就显示这段文字，否则程序会自动截取文章的部分内容作为摘要
categories: Android
tags: 安卓相关
---

## 消息提示框

```java
Toast toast=Toast.makeText(getApplicationContext(), “我是消息Toast”, Toast.LENGTH_SHORT);
toast.show();
// 第一个参数：当前的上下文环境。可用getApplicationContext()或this
// 第二个参数：要显示的字符串。也可是R.string中字符串ID
// 第三个参数：显示的时间长短。Toast默认的有两个LENGTH_LONG(长)和LENGTH_SHORT(短)，也可以使用毫秒如2000ms
```



## 查看安卓手机ip地址

```she
adb shell ip -f inet addr show wlan0
```

## 布局

```java
// scrollview
<?xml version="1.0" encoding="utf-8"?>
<ScrollView
    android:layout_height="match_parent"
    android:layout_width="match_parent"
    android:background="@color/colorTheme"
    xmlns:android="http://schemas.android.com/apk/res/android">
        
        
</ScrollView>
```

## 开一个线程

```java
new Thread() {
    @Override
    public void run() {
        //这里写入子线程需要做的工作
        makeToast("出库成功");
    }
}.start();
```

## 自定义ProgressBar

```java

```

## 线程间通信

```java

```



