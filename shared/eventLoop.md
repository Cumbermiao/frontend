# Event Loop分享

## 背景
`javascript`语言的一大特点就是单线程，也就是说同一时间只能做一件事。所以所有任务需要排队，一个任务结束以后开始另一个任务。
`HTML5`提出`Web Worker`标准，允许`javascript`脚本创建多个线程，但是子线程完全受主线程控制，且不得操作`DOM`。
于是所有任务可以分为两种，一种是同步任务，一种是异步任务。运行机制如下：
1. 所有同步任务都在主线程上执行，形成一个**执行栈**。
2. 主线程之外，还存在一个**任务队列**。只要异步任务有了运行结果，就在**任务队列**之中防止一个事件。
3. 一旦**执行栈**中所有同步任务执行完毕，系统就会读取**任务队列**，看看里面有哪些事件。那对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
4. 主线程不断重复上面的第三步。

## 什么是Event Loop？为啥要弄懂Event Loop？
`Event Loop`也就是事件循环，是指浏览器或者`Node`解决`javascript`单线程运行时不会阻塞的一种机制，这种运行方式称为“异步模式”。

1. 增加自己的技术深度，了解`javascript`的运行机制。
2. 各大互联网公司的面试，懂得原理，题目任其发挥。

*** 浏览器的`Event Loop`是在html5的规范中明确定义。`NodeJS`的`Event Loop`是基于libuv实现的。 ***


## Event Loop的任务类型
* 宏任务（`MacroTack`）
一些异步任务的回调会一次进入`macro task queue`，等待后续被调用，这些异步任务包括：
  1. `script`全部代码;
  2. `setTimeout`;
  3. `setInterval`;
  4. `setImmediate`（node的，IE10 支持）;
  5. `I/O`、`UI rendering`（浏览器独有）;
  6. `requestAnimationFrame` (浏览器独有);

* 微任务（`MicroTask`）
另一些异步任务的回调会一次进入`micro task queue`，等待后续被调用，这些异步任务包括：
  1. `process.nextTick`(node独有);
  2. `Promise`;
  3. `Object.observe`;
  4. `MutationObserver`;

## 浏览器的Event Loop完整的流程
![blockchain](https://image-static.segmentfault.com/127/150/1271505701-5a659863bb046_articlex "区块链")

1. 执行全局`Script`同步代码，这些同步代码有一些是同步语句，有一些是异步语句（如`setTimeout`就是异步语句）；
2. 全局`Script`代码执行完毕后，调用栈`Stack`会清空；
3. 从微队列`microtask queue`中取出位于队首的回调任务，放入调用栈Stack中执行，执行完后`microtask queue`长度减1；
4. 继续取出位于队首的任务，放入调用栈Stack中执行，以此类推，直到直到把microtask queue中的所有任务都执行完毕。**注意，如果在执行microtask的过程中，又产生了microtask，那么会加入到队列的末尾，也会在这个周期被调用执行**
5. `microtask queue`中的所有任务都执行完毕，此时microtask queue为空队列，调用栈Stack也为空；
6. 取出宏队列macrotask queue中位于队首的任务，放入Stack中执行；
7. 执行完毕后，调用栈Stack为空；
8. 重复第3-7个步骤；
9. 重复第3-7个步骤；
10. ......

**重点**
1. 宏队列`MacroTack`一次只从队列中取一个任务来执行，执行完后就去执行微任务队列中的任务；
2. 微任务队列`MicroTask`中所有的任务都会被依次取出来执行，直到`microtask queue`为空；


## 随堂测试
1. 
```
  console.log(1);

  setTimeout(() =>{
    console.log(2);
    Promise.resolve().then(() => {
      console.log(3)
    });
  },0);

  new Promise((resolve, reject) => {
    console.log(4)
    resolve(5)
  }).then((data) => {
    console.log(data);
  })

  setTimeout(() => {
    console.log(6);
  },0)

  console.log(7);
```
<a href="#1">答案</a>


2. 
```
  async function async1() {
    console.log('async1 start');
    await async2();
    console.log('async1 end');
  }
  async function async2() {
    console.log('async2');
  }
  console.log('script start');
  setTimeout(function () {
    console.log('setTimeout');
  }, 0);
  async1().then(
    function () { 
      console.log('async1 then') 
      }
  )
  new Promise(function (resolve) {
    console.log('promise1');
    resolve()
  }).then(function () {
    console.log('promise2');
  });
  console.log('script end');
```
<a href="#2">答案</a>  


## 参考地址
[JavaScript 运行机制详解：再谈Event Loop - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)
[带你彻底弄懂Event Loop_前端学习 - SegmentFault 思否](https://segmentfault.com/a/1190000016278115)


## 答案
1. <a name="1">1 4 7 5 2 3 6</a>
分析：
  * step 1
  ```console.log(1)```
  Stack Queue: [console]
  Macrotask Queue: []
  Microtask Queue: []
  打印结果： 1

  * step 2
  ```
    setTimeout(() =>{
      console.log(2);
      Promise.resolve().then(() => {
        console.log(3)
      });
    });
  ```
  Stack Queue: [setTimeout]
  Macrotask Queue: [callback1]
  Microtask Queue: []
  打印结果： 1

  * step 3
  ```
    new Promise((resolve, reject) => {
      console.log(4)
      resolve(5)
    }).then((data) => {
      console.log(data);
    })
  ```
  Stack Queue: [promise]
  Macrotask Queue: [callback1]
  Microtask Queue: [callback2]
  打印结果： 1 4

  * step 4 
  ``` 
    setTimeout(() => {
      console.log(6);
    })
  ```
  Stack Queue: [setTimeout]
  Macrotask Queue: [callback1, callback3]
  Microtask Queue: [callback2]
  打印结果： 1 4
  
  * step 5
  ``` console.log(7); ```
  Stack Queue: [console]
  Macrotask Queue: [callback1, callback3]
  Microtask Queue: [callback2]
  打印结果： 1 4 7

   全部`Script`代码执行完了，进入下一个步骤，从microtask queue中依次取出任务执行，直到microtask queue队列为空。 

  * step 6
  ```console.log(data) ```
  Stack Queue: [callback2]
  Macrotask Queue: [callback1, callback3]
  Microtask Queue: []
  打印结果： 1 4 7 5

  * step 7
  ```console.log(2);```
  Stack Queue: [callback1]
  Macrotask Queue: [callback3]
  Microtask Queue: []
  打印结果： 1 4 7 5 2

  但是，执行`callback1`的时候又遇到了另一个`Promise`，`Promise`异步执行完后在`microtask queue`中又注册了一个`callback4`回调函数

  * step 8
  ```
    Promise.resolve().then(() => {
      console.log(3)
    })
  ```
  Stack Queue: [promise]
  Macrotask Queue: [callback3]
  Microtask Queue: [callback4]
  打印结果： 1 4 7 5 2

  取出一个宏任务macrotask执行完毕，然后再去微任务队列microtask queue中依次取出执行

  * step 9
  ```console.log(3)```
  Stack Queue: [callback4]
  Macrotask Queue: [callback3]
  Microtask Queue: []
  打印结果： 1 4 7 5 2 3

  微任务队列全部执行完，再去宏任务队列中取第一个任务执行

  * step 10
  ```console.log(6)```
  Stack Queue: [callback3]
  Macrotask Queue: []
  Microtask Queue: []
  打印结果： 1 4 7 5 2 3 6

  以上，全部执行完后，Stack Queue为空，Macrotask Queue为空，Micro Queue为空

  Stack Queue: []
  Macrotask Queue: []
  Microtask Queue: []

  最终打印结果： 1 4 7 5 2 3 6

2. <a name="2">
  script start
  async1 start  
  async2  
  promise1  
  script end 
  async1 end  
  promise2  
  async1 then  
  setTimeout
</a>