# 问题

## 背景

我看我们项目代码的时候，发现有这种写法；

```js
export function bookShow(openId) {
    return request({
        url: '/audiobook/book/request/' + openId,
        method: 'get'
    })
}
export function bookCopy(id, book) {
    return request({
        url: '/audiobook/book/request/copy' + id,
        method: 'post',
        params: book
    })
}
```



## 过程

现在看来我之前做的总结是错误的；

我之前说，在 `axios` 中，get 请求通过 `params` 传递参数， post put 等通过 `data` 传递参数；

但是我刚看了一下文档，发现我的理解有偏差；

1. `get` 请求确实不能使用 `data` ，传递数据的时候也不能直接写成对象；

   ```js
   // 错误
   axios.get('url',{data})
   // 正确
   axios.get('url', { params: { 数据} })
   // 并且
   axios.get('/user?ID=12345')     ==     axios.get('/user', { params: {ID: 12345} });
   ```

2. post 请求也能使用 `params` 对象；

   ```js
   export function bookCopy(id, book) {
       return request({
           url: '/audiobook/book/request/copy' + id,
           method: 'post',
           params: book
       })
   }
   ```

   这我就有点不理解了，就查询了一下文档；

   ```js
   // `params` 是即将与请求一起发送的 URL 参数
   // 必须是一个无格式对象(plain object)或 URLSearchParams 对象
   params: {
       ID: 12345
   },
   // `data` 是作为请求主体被发送的数据
   // 只适用于这些请求方法 'PUT', 'POST', 和 'PATCH'
   // 在没有设置 `transformRequest` 时，必须是以下类型之一：
   // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
   // - 浏览器专属：FormData, File, Blob
   // - Node 专属： Stream
   data: {
       firstName: 'Fred'
   },
   ```

   然后我发现我之前的理解有误差；

   我之前以为 get 使用 params，post 使用 data；但是现在看他们两个传递的并不是同一种数据；

   那是不是 get 的 `{params:{}}` ，和 post 的 `{}` 传递的是一种数据呢？



## 结论 & 问题

1. 路径参数

   ![1563159420897](C:\Users\jx16081\Desktop\Gongxin\img\1563159420897.png) 

    ![1563159551843](C:\Users\jx16081\Desktop\Gongxin\img\1563159551843.png)  	

2. 查询参数

   ![1563159602577](C:\Users\jx16081\Desktop\Gongxin\img\1563159602577.png) 

![1563159613036](C:\Users\jx16081\Desktop\Gongxin\img\1563159613036.png)

3. data

   post 请求可以传递两种类型的数据；

   一是地址栏数据（路径参数 & 查询参数）；就是 拼接路径或者是`params`查询参数

   二是主体请求的 `body` ；使用 data传递；

   ```js
   // 传递 data 
   // 方法一
   http.post('url', {
   	name: '',
   	age: ''
   })
   // 方法二，通过显式的属性传递对象
   export function getVerifyCode(telephone) {
     return request({
       url: '/......',
       method: 'post',
       data: {}
     });
   }
   
   // ps： data对象使用 qs
   data: qs.stringify({
       userInfo: telephone,
   }),
   ```

   



## 不同的请求方法

```js
const http = axios.create()

// 方法一
export function bookCopy(id, book) {
   http.post("/audiobook/book/request/copy/" + id, { params: {book}})
}

// 方法二
export function bookCopy(id, book) {
   http.post(`/audiobook/book/request/copy/${id}`, { params: {book}})
}

// 方法三  
export function bookCopy(id, book) {
   http.post(`/audiobook/book/request/copy/${id}?book=${book}`,)
}

// 方法四  qs
export function bookCopy(id, book) {
   http.post(`/audiobook/book/request/copy/${id}?${qs({book})}`,)
}

```

