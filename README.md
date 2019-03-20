# jsonp-demo
### 什么是jsonp
> JSONP是服务器与客户端跨源通信的常用方法。
最大特点就是简单适用，老式浏览器全部支持，服务器改造非常小。
它的基本思想是，网页通过添加一个script元素，向服务器请求json数据，这种做法不受同源政策限制；服务器收到请求后，将数据放在一个指定的回调函数里传回来。

JSONP

请求方：frank.com 的前端程序员（浏览器）  
响应方：jack.com 的后端程序员（服务器）

请求方创建 script，src 指向响应方，同时传一个查询参数 ?callbackName=yyy   
响应方根据查询参数callbackName，构造形如yyy.call(undefined, '你要的数据')
yyy('你要的数据')这样的响应.   
浏览器接收到响应，就会执行 yyy.call(undefined, '你要的数据')
那么请求方就知道了他要的数据
这就是 JSONP

约定：

callbackName -> callback     
yyy -> 随机数 frank12312312312321325()   
jQuery 用法
```
 $.ajax({
 url: "http://jack.com:8002/pay",
 dataType: "jsonp",
 success: function( response ) {
     if(response === 'success'){
     amount.innerText = amount.innerText - 1
     }
 }
 })
```

### 实现yjq.com向mayun.com发送请求
---
1. 在yjq.com:8001处发送`http://localhost:8002/pay`请求
```
pay.addEventListener('click', function (e) {
            var script = document.createElement('script')
            document.body.appendChild(script)
            script.src = 'http://localhost:8002/pay'
            e.curretTarget.remove()
        })
```
2. 在mayun.com的server.js处修改代码：
```
if(path === '/pay'){
            response.statusCode = 200
            var number = fs.readFileSync('./db','utf-8')
            var newNumber = number - 1
            fs.writeFileSync('./db',newNumber)
            response.setHeader('Conttent-Type','application/javascript')
            response.write(`
            alert('这是来自jmayun.com的代码')
            `)
            response.end()
```
3. 最后开启两个server.js，成功接收到来自mayun.com的代码
![image.png](//video.jirengu.com/xdml/image/90943052-176f-402b-b179-468eaa667872/2019-3-20-14-47-17.png)
---
### 14. 总结
前端一开始只能使用`form`表单向服务器发送请求，但后来发现用`img`标签的`src`也发送get请求，但用`img`发送请求还需要返回一张图片，因此又使用动态地创建script元素来发送请求，衍生出了`JSONP`。由于`script`标签的`src`是可以跨域的，因此`jsonp`发送的请求也是可以跨域发送，且对老式浏览器兼容。
