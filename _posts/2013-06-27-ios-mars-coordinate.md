---
title: iOS 火星坐标问题
layout: post
tags: iOS 
---

#### 3种坐标
- [地球坐标（WGS84）](http://zh.wikipedia.org/wiki/WGS84)
    - 国际标准，GPS 标准从 GPS 设备中取出的原始数据是就是这个
    - 国际地图提供商一般使用的也是这个
- [火星坐标（GCJ-02）](http://baike.baidu.com/view/5669921.htm)
    - 中国标准，行货 GPS 设备取出的最终数据是这个
    - 加入随机的偏差
    - 国内出版的各种地图系统（包括电子形式），必须至少采用GCJ-02对地理位置进行首次加密。
- [百度坐标 (BD-09)](http://developer.baidu.com/map/question.htm#qa004)
    - 百度标准，在 GCJ-02 基础上，进行了 BD-09 二次加密
    - 需要通过坐标转换接口进行转换
    
#### iOS 现状
- 获取经纬度（GPS）
    - 火星坐标 `MKMapView`
    - 地球坐标 `CLLocationManager`
- 显示经纬度（地图）
    - 火星坐标
        - iOS 地图
        - Google 地图
        - 高德、搜搜、阿里云等
    - 地球坐标
        - Google earth（国外地图应该都是）
    - 百度坐标
        - 百度地图
        
#### 解决方案
- 国内存储用火星坐标，百度地图可以转换为火星坐标
- `CLLocationManager` 获取的坐标转换为火星坐标，算法 [CLLocation+Sino](https://gist.github.com/i0xbean/5476132)
- 几个互相转换算法
    - 地图坐标 -> 火星坐标：[地球坐标系 (WGS-84) 到火星坐标系 (GCJ-02) 的转换算法](http://blog.csdn.net/coolypf/article/details/8686588)
    - 火星坐标 <-> 百度坐标：[火星坐标系 (GCJ-02) 与百度坐标系 (BD-09) 的转换算法](http://blog.woodbunny.com/post-68.html)
    - 火星坐标 -> 地球坐标：
        - ~~iOS 私有 API `_applyChinaLocationShift:`~~（已不可用）
        - 通过不断采样的方式建立一个纠偏数据库，数据库随着采样精度的不同大小差距很大，据说有人做个了近1G的数据库。我自己用的一个100KB左右的数据库感觉效果就很不错了，采样精度为每0.5经(纬)度采样一次。目前该数据库在网上有流传。
        - 据说有人使用Matlib对数据库做了个拟合函数，本人无力没搜到这个函数，求补充
        - 使用第三方的网络API，如MapABC，原来Google也有不过现在已经关闭了。这样会使得定位依赖于网络。
        - 使用经过国家授权的校偏组件⋯⋯
        - 让用户手动修正，对于某些特定应用也许是种不错的方法。

#### Links
- [火星坐标系问题在iOS下的修正](http://yach.me/blog/2011/11/09/huo-xing-zuo-biao-xi-wen-ti-zai-iosxia-de-xiu/)
- [借助百度地图转换火星坐标的研究](http://blog.woodbunny.com/post-67.html)