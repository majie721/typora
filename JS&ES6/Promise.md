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



##### Eg.利用Promise 封装定时器

```
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







