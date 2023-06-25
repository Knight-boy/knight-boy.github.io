---
title: 斐波那契数列
date: 2020-04-25
image: /assets/img/blog/IMG_0391.JPG
description: >
  Fibonacci!
author: author2
comments: true
---

one day one game!

```js
// 1.promise
var promise =(function(n){
    return new Promise((resolve,reject) => {
        let [prev,curr] = [0,1];
        for(let i=2;i<n;i++) {
            [prev,curr] = [curr,prev+curr];
        }
        resolve(curr);
    });
});
promise(10).then((curr)=>{
    console.log(curr);
});

// 2.Generator
function* fibonacci() {
    let [prev, curr] = [0, 1];
    num = 0;
    yield prev;
    for (; ;) {
        [prev, curr] = [curr, prev + curr];
        num++;
        yield prev;
    }
}
function f(n) {
    for (var res of fibonacci()) {
        if (num >= n) break;  
    }
    console.log(res); 
}
f(9);

// 3.async
0 1 1 2 3 5...
var Fibonacci = (async function(n) {
    let [prev,curr] = [0,1];
    for(let i=0;i<n;i++) {
        [prev,curr] = [curr,curr+prev];  
    }
    await console.log(prev);
});
Fibonacci(5);
```

