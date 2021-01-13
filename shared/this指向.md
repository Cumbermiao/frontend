# this的指向问题

## 问题由来  
```   
  var obj = {
    foo: function () { 
      console.log(this.bar) 
    },
    bar: 1
  };
  var foo = obj.foo;
  var bar = 2;
  obj.foo() // 1
  foo() // 2  
```
虽然`obj.foo`和`foo`指向同一个函数，但是执行结果可能不一样。
`this`指的是函数运行时所在的环境，对于`obj.foo`来说，`foo`运行在`obj`环境，所以`this`指向`obj`；对于`foo()`来说，`foo()`运行在全局环境，所以`this`指向全局环境。

## 函数的运行环境到底是怎么决定的？
### 内存的数据结构
1. 将对象赋值给变量`obj`
  ``` var obj = { foo: 5 };```
  JavaScript 引擎会先在内存里面，生成一个对象`{ foo: 5 }`，然后把这个对象的内存地址赋值给变量`obj`。
  即变量`obj`是一个地址。后面如果要读取`obj.foo`，引擎先从`obj`拿到内存地址，然后再从该地址读出原始的对象，返回它的`foo`属性。
  ![avatar](https://www.wangbase.com/blogimg/asset/201806/bg2018061802.png)

2. 将函数赋值给变量`obj`
  `var obj = { foo: function () {} };`
  JavaScript 引擎会将函数单独保存在内存中，然后再将函数的地址赋值给`foo`属性的value属性。
  ![avatar](https://www.wangbase.com/blogimg/asset/201806/bg2018061803.png)

  由于函数是一个单独的值，所以它可以在不同的环境（上下文）执行。

## this指向的规律
* 在函数体中，简单调用函数时（非显示/隐式绑定下），严格模式下 `this` 绑定到`undefined`，否则绑定到全局对象 `window/global`；
* 一般由上下文对象调用，绑定在该对象上；
* 一般由 `bind/call/apply` 方法显示调用，绑定到指定参数的对象上；
* 一般构造函数`new`调用，绑定到新创建的对象上；
* 箭头函数中，根据外层上下文绑定的`this`决定`this`的指向。

## 四类场景
1. "test()"形式
```
  function f1(){
　　　console.log(this)  
　}
　var  arr = [f1,2,3];   
　var f2 = arr[0];
　f2();        
```
<details>
  <summary>答案</summary>
  <pre>
   this指向 window/严格模式下undefined
  </pre>
</details>
直接不带任何引用形式去调用函数，则this会指向全局对象，因为没有其他影响去改变this，this默认就是指向全局对象（浏览器是window，Node中是global）的。这个结论是在非严格模式的情况下，严格模式下这个this其实是undefined的。

2. "XXX.test()"形式
```
  var a = 1
  function test(){
    console.log(this.a)
  }
  var obj = {
    a:2,
    test
  }
  obj.test() 
```
<details>
  <summary>答案</summary>
  <pre>
   2
  </pre>
</details>
一句话，谁去调用这个函数的，这个函数中的this就绑定到谁身上。

### 测试题
```
  var a = 1
  function test(){
    console.log(this.a)
  }
  var obj = {
    a:2,
    test
  }
  var testCopy = obj.test
  testCopy()
```
<details>
  <summary>答案</summary>
  <pre><code> 
     1
  </code></pre>
</details>

```
  var a = 1
  function test(){
    console.log(this.a)
  }
  var obj = {
    a:2,
    test
  }
  var obj0 = {
      a:3,
      obj
  }
  obj0.obj.test()
```
<details>
  <summary>答案</summary>
  <pre>
  <code> 
    2
    即使是这种串串烧的形式，结果也是一样的，test()中的this只对直属上司（直接调用者obj）负责。
  </code>
  </pre>
</details>

3. "test.call(xxx) / test.apply(xxx) / test.bind()"形式
```
  var a = 1
  function test(){
    console.log(this.a)
  }
  var obj = {
    a:2,
    test
  }
  var testCopy = obj.test
  testCopy.call(obj)
```
<details>
  <summary>答案</summary>
  <pre><code> 
     2
  </code></pre>
</details>
可以看到，我们通过`call/apply`来调用`testCopy`，并且传入了你想要 `this` 指向的上下文，那么`this`就会按照你的指示行事。  

4. "new test()"形式
```
  var a = 1
  function test(a){
    this.a = a
  }
  var b = new test(2)
  console.log(b.a)
```
<details>
  <summary>答案</summary>
  <pre><code> 
     2
  </code></pre>
</details>
new这个操作符其实是new了一个新对象出来，而被new的test我们称为构造函数，我们可以在这个构造函数里定义一下将要到来的新对象的一些属性。那么在构造函数里，我们怎样去描述这个还未出生的新对象呢？没错，就是用this。所以构造函数里的this指的就是将要被new出来的新对象。

## 特别的 箭头函数
```
  var a = 1;
  var test = () => {
    var a = 3;
    console.log(this.a)
  }
  var obj = {
    a: 2,
    test
  }
  obj.test()
```
<details>
  <summary>答案</summary>
  <pre><code> 
     1
  </code></pre>
</details>
箭头函数中this对象就是**定义时所在的作用域**，也就是说箭头函数本身没有this，内部的this就是外层代码块作用域中的this。

### 测试题
```
  var a = 1
  var obj = {
    a: 2,
    test: ()=> {
      console.log(this.a)
    }
  }
  obj.test()  
```
<details>
  <summary>答案</summary>
  <pre><code> 
     1
     func在foo调用时定义，此时的foo所在作用域为obj，因此this指向obj
  </code></pre>
</details>

```
  var a = 1
  function foo(){
    var test = () => {
      console.log(this.a)
    }
    return test
  }
  var obj = {
    a : 2,
    foo:foo
  }
  obj.foo()()  
  
```
<details>
  <summary>答案</summary>
  <pre><code> 
     2
  </code></pre>
</details>


## 随机测试题
```
  var b = {
    a: 23,
    c: 3,
    d: {
      a: 78,
      e: {
        a: 100,
        f: function () {
          console.log(this.a);
        }
      }
    }
  }
  var fn = b.d.e.f;
  fn();
  b.d.e.f(); 
```
<details>
  <summary>答案</summary>
  <pre><code> 
     undefined
     100
  </code></pre>
</details>


```
  var point = { 
    x : 0, 
    y : 0, 
    moveTo : function(x, y) { 
      this.x = x;
      console.log(this.x); 
      console.log(this);   

      var moveX = function(x) { 
        this.x = x;
      }; 
      var moveY = function(y) { 
        this.y = y;
      } 
      moveX(x); 
      moveY(y); 
    } 
  }; 
  point.moveTo(1, 1); 
  console.log(point.x); 
  console.log(point.y);
  console.log(x); 
  console.log(y);
```
<details>
  <summary>答案</summary>
  <pre><code> 
     var point = { 
        x : 0, 
        y : 0, 
        moveTo : function(x, y) { 
          this.x = x;
          console.log(this.x); // 1
          console.log(this);   // point对象
          var moveX = function(x) { 
            this.x = x;
          }; 
          var moveY = function(y) { 
            this.y = y;
          } 
          moveX(x); 
          moveY(y); 
        } 
      }; 
      point.moveTo(1, 1); 
      console.log(point.x); // 1
      console.log(point.y); // 0
      console.log(x); // 1
      console.log(y);// 1
  </code>
  
  point对象下的moveTo方法中的moveX与moveX方法在调用时都是全局调用，绑定的对象都是window。
  </pre>
</details>

```
  var point = { 
    x : 0, 
    y : 0, 
    moveTo : function(x, y) { 
        var that = this; 
        var moveX = function(x) { 
            that.x = x; 
        }; 
        var moveY = function(y) { 
            that.y = y;
        } 
        moveX(x); 
        moveY(y); 
    } 
  }; 
  point.moveTo(1, 1); 
  console.log(point.x); 
  console.log(point.y); 
  console.log(x) 
  console.log(y) 
```
 <details>
  <summary>答案</summary>
  <pre><code> 
     var point = { 
        x : 0, 
        y : 0, 
        moveTo : function(x, y) { 
          var that = this; //内部变量替换
          // 内部函数
          var moveX = function(x) { 
              that.x = x; 
          }; 
          // 内部函数
          var moveY = function(y) { 
              that.y = y;
          } 
          moveX(x); //这里依然是全局调用，但是在给变量赋值时，不再是this指向，而是that指向，而that指向的对象是 point。
          moveY(y); 
      } 
    }; 
    point.moveTo(1, 1); 
    console.log(point.x); // 1
    console.log(point.y); // 1
    console.log(x) // 报错 x is not defined
    console.log(y) //
  </code></pre>
</details>   
```
const obj = {
  name: " jsCoder",
  skill: ["es6", "react", "angular"],
  say: function() {
    for (var i = 0, len = this.skill.length; i < len; i++) {
      setTimeout(function() {
        console.log("No." + i + this.name);
        console.log(this.skill[i]);
        console.log("--------------");
      }, 0);
      console.log(i);
    }
  }
};
obj.say();
```


## 参考地址
[不要再问我this的指向问题了_个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000015438195)
[JavaScript 的 this 原理 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2018/06/javascript-this.html)

