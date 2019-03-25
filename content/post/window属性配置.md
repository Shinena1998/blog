---
title: "window属性配置"
date: 2019-03-18T00:14:14+08:00
categories:
  - 微信小程序
tags:
  - window
draft: false
---

<a name="window"></a>
### window
用于设置小程序的状态栏、导航条、标题、窗口背景色。
<!--more-->
<a name="8e8aaafe"></a>
#### 全局配置
在app.json中配置
```javascript
{
  "window": {
    "navigationBarBackgroundColor": "#ffffff",
    "navigationBarTextStyle": "black",
    "navigationBarTitleText": "微信接口功能演示",
    "backgroundColor": "#eeeeee",
    "backgroundTextStyle": "light"
  }
}
```
<a name="a0aad9ad"></a>
#### 各页面配置
```javascript
{
  {
    "navigationBarBackgroundColor": "#ffffff",
    "navigationBarTextStyle": "black",
    "navigationBarTitleText": "微信接口功能演示",
    "backgroundColor": "#eeeeee",
    "backgroundTextStyle": "light"
  }
}
```
<a name="efad9190"></a>
#### js中动态配置
```javascript
wx.setNavigationBarTitle({
  title: '未结束信息',
})
```

