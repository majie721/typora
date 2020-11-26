# let 和  const

## 1.let命令

>
>
>- es6 增加let命令  和var差不多,但是let 是块级作用域,let 只在代码块里生效
>
>```js
>{
>  let a = 10;
>  var b = 10;
>}
>
>a //a is not defind
>b // 10
>```
>
>- for 循环中 let 定义的i 只在本轮循环中有效
>
>  ```js
>  // let 在for 循环中使用
>  //for 循环中 let 定义的i 只在本轮循环中有效
>  var a = [];
>  for (let i = 0; i < 10; i++) {
>    a[i] = function () {
>      console.log(i);
>    };
>  }
>  a[6](); // 6
>  ```
>
>- for 循环的特别之处(设置循环括号内的是父作用域,循环体大括号内的是一个单独的子作用域)
>
>  ```js
>  	for(let i = 0; i <3 ;i++) {
>  		let i = "abc";
>      console.log(i);
>    }
>  
>     // abc
>     // abc
>     // abc
>     //输出了3次abc
>  
>  ```
>
>  

## 2.为什么会有let

- var 的变量提升

  var 在声明前使用值为undefind,let声明的变量一定要先声明后使用,否者会报错

  ```
  // var 的情况
  console.log(foo); // 输出undefined
  var foo = 2;
  
  // let 的情况
  console.log(bar); // 报错ReferenceError
  let bar = 2;
  
  ```

  

- 暂时性死区(TDZ)

  let 声明的变量不会受到外部的影响,并且在 let申明变量之前使用就会报错

  ```
  var tmp =123
  if(true){
      tmp ='abc'; //报错, 在let声明之前使用
      let tmp;
  }
  ```

  

- 不允许重复声明

  不能在相同的作用域重复声明同一个变量,也不能从新声明参数

  ```
  // 报错
  function func() {
    let a = 10;
    var a = 1;
  }
  
  // 报错
  function func() {
    let a = 10;
    let a = 1;
  }
  
  
  function func(arg) {
    let arg;
  }
  func() // 报错
  
  function func(arg) {
    {
      let arg;
    }
  }
  func() // 不报错
  ```

  

## 3.块级作用域

### 为什要有块级作用域

ES5 只有全局作用域和函数作用域，没有块级作用域，这带来很多不合理的场景。

1.第一种场景，内层变量可能会覆盖外层变量。

```
var tmp = new Date();

function f() {
  console.log(tmp);
  if (false) {
    var tmp = 'hello world';
  }
}

f(); // undefined


```

2.第二种场景，用来计数的循环变量泄露为全局变量。

```
var s = 'hello';

for (var i = 0; i < s.length; i++) {
  console.log(s[i]);
}

console.log(i); // 5

```



## 4.const命令

- 声明一个常量,一旦声明不可更改
- 和let 一样只在块级作用域有效,同样有暂时性死区
- const  保证的是变量指向的的内存地址保存的数据不被改动,对于复合类型该变量指向的是数据的指针,因此指针指向的的数据是可变的

## .顶层对象属性

顶层对象，在浏览器环境指的是`window`对象，在 Node 指的是`global`对象。ES5 之中，顶层对象的属性与全局变量是等价的。ES6 let和const 定义的变量不在属于顶层对象的属性

```
window.a = 1;
a // 1

a = 2;
window.a // 2

let b = 3;
window.b //undefined

```



## .globalThis 对象

ES6 引入,任何环境下都能拿到顶层对象(浏览器的顶层对象就是window对象)