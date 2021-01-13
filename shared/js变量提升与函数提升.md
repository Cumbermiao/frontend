# js变量提升与函数提升

##先来看两个例子，下面的两段代码分别输出什么？
```
// 代码段1
function foo() {
  var a = 1;
  function a() {}
  console.log(a);
}
foo();

// 代码段2
function foo() {
  var a;
  function a() {}
  console.log(a);
}
foo();

```
答案是：代码段1打印的是1，代码段2打印的是 a() 函数。

## 变量提升 

### js是怎么创建变量的呢？
```
var a = 1;
var b = 2;
```
js在解析上面的代码的时候，其实会按照下面的方式进行解析的：
```
var a;
var b;
a = 1;
b = 2;
```
也就是js会先把所有变量都声明好了之后，然后才进行赋值，并不是声明一个变量就赋值，再声明一个再赋值。js所谓变量提升，提升就是为了事先声明变量。

再举个例子:
![RUNOOB 图标](https://image-static.segmentfault.com/353/839/3538399571-58ba74ad74f89_articlex)

## 函数提升
javascript中不仅仅是变量声明有提升的现象，函数的声明也是一样；具名函数的声明有两种方式：
1. 函数声明
```
function f(){
  console.log(0)
}
```  
2. 函数表达式
```
var fn = function(){
  console.log(1)
}
```

例子：
```
f(); //1
fn();//fn is not a function 
//函数表达式
var fn = function(){
    console.log(1)
}
//函数声明
function f(){
    console.log(0)
}
```
这里把代码还原，我们来看一下：
```
function f(){
  console.log(0)
}
var fn;
f();
fn();
fn = function(){
​  console.log(1)
}
```
在非严格模式下，代码块中，只有使用 var 声明的变量和函数声明是可以提升的，但是函数声明只能将函数的名字提升出去。

#### 实例
```
console.log(foo);
function foo(){
  console.log("函数声明");
}
var foo = "变量";
```
输出为：
```
function foo(){
  console.log("函数声明");
}
```
代码实际为：
```
function foo(){
  console.log("函数声明");
}
var foo;
console.log(foo);   
foo = "变量";
```

函数提升优先级比变量提升要高，且不会被变量声明覆盖，但是会被变量赋值覆盖。
在最后再加上打印就能看到函数已经被覆盖了。

```
function f() {
    console.log(typeof f); //function
    f = 3;
   console.log(typeof f); //number
};

f();

var s = function s() {
    console.log(typeof s); //function
    // var s = 3;
    s = 3;
    console.log(typeof s); //function 
};

s();
```
上述代码中，函数f是具名函数，函数s是函数表达式。具名函数中，可以在函数内部改变函数名，而函数表达式，如果有函数名，则它的函数名只能作用在其自身作用域中，且不可改变改变函数名。





