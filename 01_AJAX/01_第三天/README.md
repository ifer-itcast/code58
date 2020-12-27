## 模板引擎的原理

```js
// #1 准备的模板
let str = '<div>{{name}}今年{{ age }}岁了</div>'

// #2 准备数据
const data = {
    name: 'ifer',
    age: 18
};

// #3 template 方法内部的处理
// 这个地方要加 g，代表全局匹配
let pattern = /{{\s*([a-zA-Z]+)\s*}}/g;

str = str.replace(pattern, function(大正则, 第一个小分组) {
    console.log(大正则, 第一个小分组);
    // 你这里返回啥，就会把大正则匹配到的内容替换成啥
    return data[第一个小分组];
});

// #4 处理完之后的结果，就是拼接好之后的字符串
console.log(str);
```

## 发送请求

```js
// 1. 创建 XHR 对象
const xhr = new XMLHttpRequest();

// 2. 调用 open 函数，请求方式、请求地址
xhr.open('GET', 'http://www.liulongbin.top:3006/api/getbooks');

// 3. 调用 send 函数
xhr.send();

// 4. 监听 onreadystatechange 事件
xhr.onreadystatechange = function () {
    // readyState 是 xhr 对象状态码，4 代表数据已经接受完毕了
    // xhr.status 是 HTTP 状态码，200 代表 HTTP 响应成功
    if (xhr.readyState === 4 && xhr.status === 200) {
        // 获取服务器响应的数据
        // xhr.responseText 里面的 status 是后端返回的数据的一部分，有后端决定的
        console.log(xhr.responseText);
    }
};
```

## xhr 对象的 readyState 状态码

面试必会

0. 

1. 

2. 

3. 

4. 

## POST 请求

```js
// 1. 创建 xhr 对象
const xhr = new XMLHttpRequest();
// 2. 调用 open 函数
xhr.open('POST', 'http://www.liulongbin.top:3006/api/addbook');
// 3. 设置 Content-Type 属性
// 设置请求头当中的 Content-Type 为 application/x-www-form-urlencoded
// 必须放到 open 之后和 send 之前
// application/x-www-form-urlencoded 这个对应的数据格式就是 key=value&key=value
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
// 4. 调用 send 函数
// POST 请求传递的数据必须通过 send 里面的参数来传递
xhr.send('bookname=水浒传1&author=施耐庵1&publisher=上海图书出版社1');
// 5. 监听事件
xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
        console.log(xhr.responseText);
    }
};
```

## XML 和 HTML 和 JSON

HTML: 【超文本】标记语言，展示网页的

XML: 【可扩展】标记语言，传输数据的

JSON: 用来传输数据的一种格式（是数组和对象的字符串的表示形式），解决了 XML 的一些问题（臃肿、解析不方便的问题）

## JSON.stringify 调试的技巧

```js
// 第三个参数：一个字符串或数字对象，用于在输出JSON字符串中插入空白，以提高可读性
JSON.stringify(r, null, 4);
```

序列化：对象转 JSON 格式的字符串（JSON.stringify）

反序列化：字符串格式的 JSON 转对象（JSON.parse）

## 封装 AJAX

```js
// 使用
itheima({
    method: 'post',
    url: 'http://www.liulongbin.top:3006/api/addbook',
    data: {
        bookname: '水浒传2',
        author: '施耐庵2',
        publisher: '北京图书出版社2'
    },
    success: function (res) {
        console.log(res)
    }
});
```

```js
// 封装
;(function() {
    function resolveData(data) {
        const arr = [];
        for(let attr in data) {
            // console.log(attr, data[attr]);
            arr.push(`${attr}=${data[attr]}`);
        }
        return arr.join('&');
    }
    
    // 函数封装不要调用者决定做什么事情
    // resolveData({ name: 'zs', age: 20 })
    
    // let res = resolveData({ name: 'zs', age: 20 })
    // console.log(res)
    
    function itheima(options) {
        const xhr = new XMLHttpRequest();

        xhr.timeout = 3000;
        xhr.ontimeout = function() {
            alert('数据请求超时');
        };
    
        // 把外界传递过来的参数对象，转换为 查询字符串
        // bookname=水浒传&author=施耐庵
        let qs = resolveData(options.data);
    
        if (options.method.toUpperCase() === 'GET') {
            // 发起GET请求
            xhr.open(options.method, options.url + '?' + qs);
            xhr.send();
        } else if (options.method.toUpperCase() === 'POST') {
            // 发起POST请求
            xhr.open(options.method, options.url);
            xhr.setRequestHeader(
                'Content-Type',
                'application/x-www-form-urlencoded'
            );
            xhr.send(qs);
        }
    
        xhr.onreadystatechange = function () {
            if (xhr.readyState === 4 && xhr.status === 200) {
                var result = JSON.parse(xhr.responseText);
                options.success(result);
            }
        };
    }
    
    window.itheima = itheima;
})();
```

## XHR Level2

