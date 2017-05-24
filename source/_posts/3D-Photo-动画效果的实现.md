---
title: 3D-Photo 动画效果的实现
date: 2017-02-07 11:46:43
---

Video 转换 Images

触摸或者倾斜屏幕

实现动态切换的3dPhoto效果

<!--more-->
## 实现方法的选择
#### Fyuse

1. video 转换为 images，格式为二进制数据，顺序存放数据库内
2. 发起异步请求，获取所有images的二进制格式数据（每一张image均需要一个请求）
3. 图片数据创建为Blob对象（js中用于处理二进制数据的对象），转换生成base64格式的url地址
4. canvas写入src＝url地址，绘制渲染对应的image

#### Dobby 流光拾影

1. video 转换为 images，存入cdn地址
2. js发起请求，获取所有图片的http地址，html结构中插入`display:none;`的img标签，等待所有图片download完成
3. js控制图片的显示切换

#### Our CES Orbit

1. video 转换为 images，存入cdn地址
2. html模版加载切片图片cdn地址的第一张图片`****00001.jpg`，同时获取此视频切片的总数量`ListCount == xxx`
3. 图片地址字符串拼接获取所有images的cdn地址，html插入img标签`<img src='****00022.jpg'>`，loading等待所有图片load完成
4. js控制图片切换顺序

## 移动端横竖屏翻转
假如页面设计为仅展示横屏模式，需要对页面横竖屏翻转做特殊处理，实现方法根据不同的设备类型略有不同
#### 样式处理
媒体查询，获取设备横竖屏状态，设置body样式翻转角度

```
@media screen and (orientation:landscape) {
    .orientationChange {
        transform: rotate(0deg);
        -ms-transform: rotate(0deg);
        -webkit-transform: rotate(0deg);
        -o-transform: rotate(0deg);
        -moz-transform: rotate(0deg);
    }
}
@media screen and (orientation:portrait) {
    .orientationChange {
        transform: rotate(90deg);
        -ms-transform: rotate(90deg);
        -webkit-transform: rotate(90deg);
        -o-transform: rotate(90deg);
        -moz-transform: rotate(90deg);
    }
}
```

#### 事件绑定
Firefox 对于`onorientationchange`事件的支持不好，因此使用媒体查询属性替代实现
Iphone 设备完全支持`onorientationchange`事件
Android 设备使用`resize`事件来监听屏幕翻转

```
// 火狐浏览器适配
var userAgent = window.navigator.userAgent.toLowerCase();
if(userAgent.indexOf("fxios") >= 1 || userAgent.indexOf("firefox") >= 1){
    var mqOrientation = window.matchMedia("(orientation: portrait)");
    mqOrientation.addListener(function() { updateOrientation() });
}else {
    if(device.ios()) {
        // iphone
        window.addEventListener("onorientationchange" in window ? "orientationchange" : "resize", function() {
            updateOrientation();
      }, false);
    }else if(device.android()){
        // android
        window.addEventListener("resize", function() {updateOrientation()}, false);
    }
}
```

#### 事件监听处理

```
// 设置body width height
function updateOrientation() {
    // 个别浏览器在翻转事件触发300ms后才可获取到此时设备真实的width、height值
    setTimeout(function() {
        var winW = $(window).width();
        var winH = $(window).height();
        if (window.orientation === 180 || window.orientation === 0) {
            $('body').css({
                'width' : winH + 'px',
                'height' : winW + 'px',
                'top' : winH / 2 - winW / 2 + 'px',
                'right' : winH / 2 - winW / 2 + 'px'
            });
        }else if (window.orientation === 90 || window.orientation === -90 ){
            $('body').css({
                'width' : winW + 'px',
                'height' : winH + 'px',
                'top' : '0',
                'right' : '0'
            });
        }
        if(device.android()) {
            // 部分android设备在设置body top/right值后无法重新渲染页面
            delayRander();
        }
    }, 300);
}

// 适配安卓机型，延缓10毫秒重绘body
function delayRander() {
    var originalBodyStyle = getComputedStyle(document.body).getPropertyValue('display');
    document.body.style.display = 'none';
    setTimeout(function () {
        document.body.style.display = originalBodyStyle;
    }, 10);
}
```

#### 补充
Iphone Safari 浏览器横屏状态隐藏搜索栏的方法

```
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="apple-mobile-web-app-capable" content="yes"/>
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<body onload="setTimeout(function() { window.scrollTo(0, 1) }, 100);"></body>
```

## 重力感应切换图片
#### 事件绑定

```
// 是否支持deviceorientation
if (window.DeviceOrientationEvent) {
    window.addEventListener("deviceorientation", shakeEventHandler, false);
}
```

#### 事件处理

```
// 根据事件提供的欧拉角数据处理重力感应
// 欧拉角数据较为直观，容易理解，但缺点在于受到万向锁的限制，会出现数据突变，难以实现平滑旋转
// 后期优化方案有两种：旋转矩阵和四元数（倾向于使用四元数）

// 弧度值
var degtorad = Math.PI / 180;

function shakeEventHandler(event) {
    var absolute = event.absolute;
    var curA = event.webkitCompassHeading ? (360 - event.webkitCompassHeading) : event.alpha;
    var curB = event.beta;
    var curG = event.gamma;
    // 欧拉角转换为弧度值处理0-360的数据切换
    var aveA = Math.sin((curA - Math.abs(curB)) * degtorad);
    var aveB = curB;
    var aveG = Math.abs(curG);
    refreshImgWin(aveA, aveB, aveG);
}
```

#### 优化
低通滤波优化切换稳定性，，降低由于微小抖动引起的切换

```
// 简单的低通滤波，参数c为当前输入值，参数p为上一次输出值，参数w为可调参数
function mathWave(c, p) {
    var w = 0.8;
    return (1 - w) * c + w * p;
}
```


