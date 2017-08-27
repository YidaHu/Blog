---
title: Android之首次加载引导界面
date: 2016-02-09 23:24:28
categories: Android
tags:
- 引导界面
---

## 前言

现在好多优秀的应用（举例QQ、UC浏览器）都采用了欢迎页面与使用向导的方式给用户带来了良好的用户体验。

一般来说用户第一次安装应用或者安装了新版本后第一次进入应用都会显示成 欢迎页面-使用向导-主界面 的方式

用户没有安装新版本或者不是第一次进入的时候都会显示成 欢迎页面-主界面的方式

想要实现这种不同的分支，我们就要使用一种变量来存储我们是否是第一次进入应用，当然这种变量不可能是存储在应用里，而是要存储在应用包名底下的文件中

<!--MORE-->

那么我们就来看看实现这种变量存储和修改的步骤吧

```
/**
         * 第一次启动时，因为没SharedPreferences文件，所以为初始化值，比如true要显示，然后在将这个值赋为false，
         * 保存后，下次启动是读取SharedPreferences文件，找到值就为false。
         */
        preferences = this.getSharedPreferences("share", MODE_PRIVATE);
        boolean isFirstRun = preferences.getBoolean("isFirstRun", true);
        SharedPreferences.Editor editor = preferences.edit();
        //判断是否第一次加载引导界面
        if (isFirstRun) {
            editor.putBoolean("isFirstRun", false);
            editor.commit();
            Intent intent = new Intent(MainActivity.this, GuideActivity.class);
            startActivity(intent);
            finish();
        }
```



