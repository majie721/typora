目录

[TOC]

#### 基本语法

```javascript
function * foo (){
 	yield 'hello';
}  
```

>1.function 和函数名中间有一个*号
>
>2.函数内部 有yield关键字
>
>3.函数返回的结果是一个迭代器对象,需要用迭代器的next()方法进行迭代.next() 方法返回是当前迭代的value和是否结束的done组成的对象.done为true时表示迭代已完成

------



#### yiled

```
    function * foo(){
        yield 1+2;
        yield "hello";
        yield "world";
        return "end";
    }

    let data = foo();
    console.log(data.next()); //{value:3,done:false} //只有next()指向这里的时候才会求值
    console.log(data.next()); //{value:"hello",done:false}
    console.log(data.next()); //{value:"world",done:false}
    console.log(data.next()); //{value:"end",done:true}  迭代完成 done 变成true

```

>1.next()  遇到yeild就会停下,并返回yield 后面表达式的值作为返回对象的value的值
>
>2.下一次next(),从上一次停止的地方开始.直到遇到下一个yield 或者return 语句.

------

####  for ...of 循环

>for...of 循环不需要手动next()就能自动完成Generator的迭代
>
>一旦 next() 方法返回的done等于true时,循环就会结束

```
function * foo(){
        yield 1+2;
        yield "hello";
        yield "world";
        return "end";
    }

   let data = foo()
   for (const val of data) {
        console.log(val)    //3,hello,world
   }
```



##### 高级用法(待完成3前 月份)