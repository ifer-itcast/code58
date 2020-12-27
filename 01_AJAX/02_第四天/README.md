## FormData 基本使用

[参考 MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData)

```js
// 1. 创建 FormData 实例
const fd = new FormData()
// 2. 调用 append 函数，向 fd 中追加数据
fd.append('uname', 'zs')
fd.append('upwd', '123456')

const xhr = new XMLHttpRequest()
// 后端这个接口支持接收和解析 FormData 格式的数据
xhr.open('POST', 'http://www.liulongbin.top:3006/api/formdata')
// 发送 formData 格式的数据的时候，不要设置请求头中的 Content-Type，浏览器识别到这种 FormData 数据格式，会自动加
xhr.send(fd)

xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
        console.log(JSON.parse(xhr.responseText))
    }
}
```

## FormData 收集表单数据

```js
// 1. 通过 DOM 操作，获取到 form 表单元素
const form = document.querySelector('#form1')

form.addEventListener('submit', function (e) {
    // 阻止表单的默认提交行为
    e.preventDefault()
    // 创建 FormData，快速获取到 form 表单中的数据
    // 收集数据的时候到底是使用  $(this).serialize(); 还是 FormData，取决于后端的接口
    const fd = new FormData(form)

    const xhr = new XMLHttpRequest()
    xhr.open('POST', 'http://www.liulongbin.top:3006/api/formdata')
    xhr.send(fd)
    xhr.onreadystatechange = function () {
        if (xhr.readyState === 4 && xhr.status === 200) {
            console.log(JSON.parse(xhr.responseText))
        }
    }
})
```

## 上传进度

```js
xhr.upload.onprogress = function(e) {
    // 监听上传过程
    // e.loaded => 当前上传的数据
    // e.total => 总数据
};
xhr.upload.onload = function() {
    // 上传完毕会触发
};
```

## jQuery 上传文件注意点

```js
$.ajax({
    method: 'POST',
    url: 'http://www.liulongbin.top:3006/api/upload/avatar',
    data: fd,
    // #1 不要编码数据
    processData: false,
    // 默认情况下 jQuery 会自动在请求头当中设置 Content-Type: application/x-www-form-urlencoded;
    // #2 去掉默认的设置，因为 FormData 数据的信息不需要手动加
    contentType: false,
    // #3 两个方法，如果说指向监听当前的请求开始和请求结束做一些事情，用下面两个方法
    beforeSend() {
        $('#loading').show();
    },
    complete() {
        $('#loading').hide();
    },
    success: function (res) {
        console.log(res)
    },
    // #4 jQuery 本身没有提供上传进度的 API，需要通过下面的方式去实现
    xhr: function () {
        const xhr = new XMLHttpRequest();
        xhr.upload.onprogress = function (e) {
            if (e.lengthComputable) {
                // 计算出上传的进度
                let procentComplete = Math.ceil((e.loaded / e.total) * 100)
                // console.log(procentComplete)
                // 动态设置进度条
                oProgress.value = procentComplete;
            }
        }
        return xhr;
    }
})
```

```js
// 会监听所有的请求开始
$(document).ajaxStart(function () {
    $('#loading').show()
});

// 监听到所有的 Ajax 完成的事件
$(document).ajaxStop(function () {
    $('#loading').hide()
});
```

## 同源

1\. 协议、域名、端口号都一样称为同源，否则称为非同源（跨域）

2\. http 协议端口号默认是 80，https 端口号默认是 443

3\. JSONP 原理: 利用 script 标签不受跨域限制的这一特点来实现的，其实后端返回的是一个函数调用，真正的数据通过函数的实参传递过来，前端只需要准备相同名字的函数，在形参当中就可以取到后端传递的数据啦！

4\. 其实平常用 script 的 src，图片的 src，link 标签的 href 本质上对于服务器来说都是发起了一个 GET 请求

## jQuery 发起 JSONP 请求

```js
function abc(r) {
    console.log(r);
}

$.ajax({
    url: 'http://www.liulongbin.top:3006/api/jsonp?name=zs&age=20',
    // 代表我们要发起JSONP的数据请求
    dataType: 'jsonp',
    // 默认是 callback，可以通过 jsonp 属性执行这个名字，一般不需要操作
    // jsonp: 'cb',
    // 可以指定回调函数的名字，一般也不需要操作
    // jsonpCallback: 'abc',
    success: function (res) {
        console.log(res)
    }
})
```

