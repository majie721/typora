#### Js 自定义错误

```javascript
//自定义错误
    class HttpError extends Error{
        constructor(msg){
            super(msg)
            this.name = 'http请求错误'
        }
    } 
    


    new Promise((resove,reject)=>{
        reject(new HttpError)
    }).then((res)=>{
        console.log(res)
    }).catch(err=>{
        console.log(err.name)
    })
```



>1.自定义错误集成Js Error
>
>2.构造函数constructor(msg)  里要调用父类的构造方法super(msg)