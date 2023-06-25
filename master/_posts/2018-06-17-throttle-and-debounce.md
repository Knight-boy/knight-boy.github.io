---
title: 防抖和节流
date: 2018-06-17
image: /assets/img/blog/IMG_0392.JPG
description: >
  throttle and debounce!
author: author2
comments: true
---

one day one game!

```js
// 节流
function throttle(fn, interval) {
    var timer,
        isFirst = true;
    return function() {
        var args = arguments,
            that = this;
        if(isFirst) {                            //如果是第一次调用，不许延迟执行；
            fn.apply(that, args)
            return isFirst = false;
　　　　 }
        if(timer) {                              //如果定时器还在，说明前一次延迟执行还未完成；
            return false;
        }
        timer = setTimeout(() => {               //延迟一段时间后执行；
            clearTimeout(timer);
            timer = null;
            fn.apply(that, args);
        }, interval || 500);
    }
}
window.onresize = throttle(function() {
    console.log(1);
}, 1000);

// 防抖
function debounce(fn, interval) {
    var timer,
        isFirst = true,
        can = false;
    return function() {
        var args = arguments,
            that = this;
        if(timer) {
            clearTimeout(timer);
            timer = null;
        }        
        if(isFirst) {
            fn.apply(that, args);
            isFirst = false;
            setTimeout(() => {
                can = true;
            }, interval || 1000);
　　　　 }
        else if(can) {
            timer = setTimeout(() => {
                fn.apply(null, args);
            }, interval || 1000);
        }
    }
}
```

