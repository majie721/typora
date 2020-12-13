目录

[TOC]

#### Promise

##### Eg.利用Promise异步加载图片

```javascript
function loadImage(src){
  return new Promise((resolve,reject)=>{
    const image = new Image();
    image.src = src;
    image.onload = ()=>{
      resolve(image)
    };
    image.error=reject;
  })
}

loadImage("../pic/pic1.png").then((image)=>{ //加载完成后对图片进行处理
  document.body.appendChild(image);
  image.style.width="200px";
})
```



##### Eg.利用Promise 封装定时器1

```javascript
function timeout(dalay=1000){  //封装一个定时器
		return new Promise(resolve=>setTimeout(resolve,dalay))
	}
	
	timeout(2000).then(_=>{
		console.log("done");
		return timeout(3000);  //继续返回Promise
	}).then(_=>{
		console.log("done2")
	})
```

##### Eg.利用Promise封装定时器2

```
function intval(dalay,callback){
       return new Promise(resolve=>{
           let id = setInterval(() => {
               callback(id,resolve)
           }, dalay);
       })
   }
   
   let i = 0;
   intval(1000,(id,resolve)=>{
       i++;
       console.log(i,id,resolve)//1,2,3....10
       if(i>=10){
           clearInterval(id)  //清除定时器
           resolve(i)         //改变Promise的状态 v
       }
       
   }).then((i)=>{
       intval(500,(id,resolve)=>{
           i--;
           console.log(i,id,resolve)  //9,8,7....0
           if(i<=0){
               clearInterval(id)
               resolve(i) 
           }
       })
   })
```

##### Eg.利用Promise  封装ajax

```javascript
function query(url){
  return new Promise((resolve,reject)=>{
    let http =  new XMLHttpRequest();
    http.open('GET',url)
    http.send();
    http.onload = function(){
      if(this.status == 200){
        resolve(this.response)    
      }else{
        reject("请求错误,http status:"+this.status)
      }
    };
    http.onerror = err =>reject(err);
  })
}

query("http://localhost:8888/all") 
  .then(res=>{
  console.log(res)
}).catch(err=>{
  console.log(err)
})
```



------

#### Promise.resolve()

> 将现有对象转换成promise对象
>
> 注意:当参数是thenable对象时,会立即执行thenable对象的then()方法



------

#### Promise.reject()

>等同于  new Promise((resolve,reject)=>reject())
>
>注意:当参数是thenable对象时,catch的结果是theanble对象,而不是thenable对象then方法的结果.这里和Promise.resolve()是不一样的

```javascript
    let thenable1 = {
        then:function(resolve,reject){
               resolve("success") 
        }
    }

    let p = Promise.resolve(thenable1)
    p.then(res=>console.log(res)) // success

    let thenable2 = {
        then:function(resolve,reject){
             reject('faild')   
        }
    }

    let p2 = Promise.reject(thenable2);
    p2.catch(err=>{
        console.log(err)  //thenable 对象  注意:区别Promise.resolve()
    })
```

------



#### Promise.all() 

>Promise.all() 将多个promise包装成一个新的Promise的实例
>
>var p = Promise.all([p1,p2,p3])  当p1,p2,p3都fullfilled p为fullfilled,只有p1,p2,p3中只要有一个rejected  p的状态就为rejected

```javascript
	function query(url){
        return new Promise((resolve,reject)=>{
            let http = new XMLHttpRequest();
            http.open("GET",url)
            http.send();
            http.onload = function(){
                if(this.status ==200){
                    resolve(this.response)
                }else{
                    reject("http error")
                }
            };
            http.onerror = ()=>reject("网络错误")
        })
    }


    let promises = [1,2].map((id)=>query(`http://localhost:8888/detail?id=${id}`))
    promises.push(query("http://localhost:8888/book"))
    console.log(promises)
    Promise.all(promises).then((res)=>{
        console.log(res) //按数组顺序返回请求的结果 [{detail1},{detail2},{book}]
    }).catch(err=>{
        console.log(err)
    })
```

------

#### Promise.allSettled()

> 和Promise不一样的是 会忽略reject ,状态都是fullFilled

------

#### Promise.race()

>将多个promise实例包装成一个promise
>
>let p = Promise.race([p1,p2,p3])   只要p1,p2,p3中任意一个实例的状态变化,p实例就会跟着变,并将率先变化的promise的返回传递给p回调函数

```javascript
//请求超时的例子
  function query(url){
        return new Promise((resolve,reject)=>{
            let http = new XMLHttpRequest();
            http.open("GET",url)
            http.send();
            http.onload = function(){
                if(this.status ==200){
                    resolve(this.response)
                }else{
                    reject("http error")
                }
            };
            http.onerror = ()=>reject("网络错误")
        })
    }



   let p1 = query("http://localhost:8888/detail?id=2");
   let p2 = new Promise((resolve,reject)=>{
        setTimeout(()=>reject("请求超时"),3000)
    })

   let p = Promise.race([p1,p2])
   
   p.then(res=>{
        console.log(res)
    }).catch(err=>{
        console.log("catch error:"+err)
    })

```

------

#### Promise队列

##### 原理

> then 里面返回promise,下一个then需要等待上一次返回的promise的状态变化

```javascript
	  let p  = Promise.resolve('start')   
    p =  p .then(res=>{
        return new Promise(resolve=>{ //then 里面返回promise
            setTimeout(()=>{
                console.log(1);
                resolve()
            },1000)
        })
    })

    p.then(res=>{ //这里的then 需要等待上一个promise resolve更改状态
        return new Promise(resolve=>{
            setTimeout(()=>{
                console.log(2);
                resolve()
            },1000)
        })
    })
```



##### eg1. Map 函数实现promise队列

```javascript
	function queue(arr){
        let p = Promise.resolve("start")
        arr.map((v)=>{
            p = p.then(res=>{
                return new Promise((resolve)=>{
                    setTimeout(()=>{
                        console.log(v)
                        resolve(v)
                    },1000)
                })
            })    
        })
    }

    queue([1,2,3,4,5])
```

##### eg2. reduce函数实现promise队列

```javascript
	function queue2(arr){
        let p = Promise.resolve('start2');

        arr.reduce((promise,val)=>{
            p = p.then(res=>{
                return new Promise(resolve=>{
                    setTimeout(()=>{
                        console.log(res);
                        resolve(val)
                    },1000)
                })
            })
        },p)
    }
    queue2([1,2,3,4,5])
```

