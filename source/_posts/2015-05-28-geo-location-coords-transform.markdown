---
title: 电子地图地理坐标系统及相关算法的JavaScript实现
date: 2015-05-28 15:22:00
tags: LBS
categories: Web-FE
sidebar: false
---
在开发LBS相关应用的时候，如果不了解国内对于电子地图的另外一套坐标系统，就可能掉进坑里不能自拔。简记自己参与的项目中遇到这个坑时做的调研工作，以及使用的一些算法。这些算法在其他博客里以C++或者Java实现，这里使用JavaScript实现，以供使用各家地图JavaScript API时参考。

目前的方法误差都较大（10米量级），等待更好的拟合公式吧。

<!-- more -->

## 三种坐标系统的概念

① __WGS84__
World Geodetic System 1984，即世界大地坐标系。GPS所采用的坐标系是美国国防部1984年世界大地坐标系，简称WGS84。

② __GCJ-02坐标系__
GCJ-02坐标系，即火星坐标系统。这是中国境内使用的针对电子地图的国家保密坐标系统。其对真实的地理坐标（例如GPS坐标）进行人为的加偏处理。其初衷似乎是为了保护国家安全。不过火星坐标也是很容易就可以转换为WGS84坐标系统的，所以很难说有什么“保密”作用在里头。

③ __BD-09__
百度地图在火星坐标系的基础上又一次进行了偏移处理。形成自己的坐标系统，BD-09。百度地图服务对此有所介绍，[百度LBS开放平台常见问题](http://developer.baidu.com/map/question.htm#qa0043)：

> 2.2 百度采用何种坐标体系？
> 百度地图api中采用两种坐标体系，经纬度坐标系和墨卡托投影坐标系。前者单位是度，后者单位是米，具体定义可以参见百科词条解释：[http://baike.baidu.com/view/61394.htm](http://baike.baidu.com/view/61394.htm) 和 [http://baike.baidu.com/view/301981.htm](http://baike.baidu.com/view/301981.htm) 。

## 各地图服务商所采用的坐标系统

+ 火星坐标系： 高德地图，搜狗地图，谷歌地图，腾讯地图
+ 百度坐标系： 百度地图

## WGS to GCJ-02
下面的代码给出了将原始GPS经纬度坐标转换为火星坐标系下的坐标的方法。

```javascript
var PI = Math.PI;
var A = 6378245.0;  /* 地球平均半径，米 */
var EE = 0.00669342162296594323;
var X_PI = PI * 3000.0 / 180.0;
    
/**
 * 判断是否在中国境内
 */
function outOfChina(lon, lat){
    if (lon < 72.004 || lon > 137.8347){
        return true;
    }

    if (lat < 0.8293 || lat > 55.8271){
        return true;
    }

    return false;
}
    
/**
 * 纬度偏移
 */
function transformLat( x,  y){
    var ret = -100.0 + 2.0 * x + 3.0 * y + 0.2 * y * y + 0.1 * x * y + 0.2 * Math.sqrt(Math.abs(x));
    ret += (20.0 * Math.sin(6.0 * x * PI) + 20.0 * Math.sin(2.0 * x * PI)) * 2.0 / 3.0;
    ret += (20.0 * Math.sin(y * PI) + 40.0 * Math.sin(y / 3.0 * PI)) * 2.0 / 3.0;
    ret += (160.0 * Math.sin(y / 12.0 * PI) + 320 * Math.sin(y * PI / 30.0)) * 2.0 / 3.0;
    return ret;
}
  
/**
 * 经度偏移
 */
function transformLon( x,  y){
    var ret = 300.0 + x + 2.0 * y + 0.1 * x * x + 0.1 * x * y + 0.1 * Math.sqrt(Math.abs(x));
    ret += (20.0 * Math.sin(6.0 * x * PI) + 20.0 * Math.sin(2.0 * x * PI)) * 2.0 / 3.0;
    ret += (20.0 * Math.sin(x * PI) + 40.0 * Math.sin(x / 3.0 * PI)) * 2.0 / 3.0;
    ret += (150.0 * Math.sin(x / 12.0 * PI) + 300.0 * Math.sin(x / 30.0 * PI)) * 2.0 / 3.0;
    return ret;
}
  
/**
 * 转换函数，输入：WGS坐标的经纬度
 */
function transformGPS2Mars(wgLon, wgLat){
    var mgLon, mgLat;

    if (outOfChina(wgLon, wgLat)){
        mgLat  = wgLat;
        mgLon = wgLon;
    }else{
        var dLat = transformLat(wgLon - 105.0, wgLat - 35.0);
        var dLon = transformLon(wgLon - 105.0, wgLat - 35.0);
        var radLat = wgLat / 180.0 * PI;
        var magic = Math.sin(radLat);
        magic = 1 - EE * magic * magic;
        var sqrtMagic = Math.sqrt(magic);
        dLat = (dLat * 180.0) / ((A * (1 - EE)) / (magic * sqrtMagic) * PI);
        dLon = (dLon * 180.0) / (A / sqrtMagic * Math.cos(radLat) * PI);
        
        var mgLat = wgLat + dLat;
        var mgLon = wgLon + dLon;
    }  

    console.log('WGS-84: ( ', wgLon, ', ', wgLat, ') ==> GCJ-02: (', mgLon, ', ', mgLat, ')');

    return {
        lonMars: mgLon,
        latMars: mgLat
    };
}
```


## GCJ-02 to BD-09

```javascript
/* 火星坐标 到 百度地图坐标的转换算法
 * input: 
 *   gg_lon, GCJ-02 longitude，火星坐标系经度
 *   gg_lat, GCJ-02 latitude， 火星坐标系纬度
 * return:
 *   百度坐标
 */
function bd_encrypt(gg_lon, gg_lat){
    var X_PI = Math.PI * 3000.0 / 180.0;  
    var x = gg_lon, y = gg_lat;  
    var z = Math.sqrt(x * x + y * y) + 0.00002 * Math.sin(y * X_PI);  
    var theta = Math.atan2(y, x) + 0.000003 * Math.cos(x * X_PI);  
    var bd_lon = z * Math.cos(theta) + 0.0065;  
    var bd_lat = z * Math.sin(theta) + 0.006; 

    return {
        bd_lat: bd_lat,
        bd_lon: bd_lon
    };
}
```

## 测试验证
终端设备会上传自己的GPS坐标以及相应的经过内置算法加密后的火星坐标。

### GPS坐标 -> 火星坐标
使用上面实现的算法对某坐标进行转换，结果与设备自己上传的火星坐标相比，可以保证1E-04的精确度。小数点后第5位有所不同。

### 火星坐标 -> 百度坐标
将根据上面的算法得到的计算结果，与百度提供的坐标转换的结果相比，也是有1E-04的误差。估算对应的实际距离：

```
误差 ≈ 6378245 * Math.PI / 180.0 * 1E-04 = 11.13米
```
也就是说此方法与百度自己提供的方法相比，有11米左右的转换偏差。