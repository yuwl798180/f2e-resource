# Fetch

---

<!-- TOC depthFrom:2 -->

- [概述](#概述)
  - [检测浏览器是否部署](#检测浏览器是否部署)
  - [简单示例](#简单示例)
- [stream 数据流](#stream-数据流)
- [Fetch 引入的 Headers 对象](#fetch-引入的-headers-对象)
- [Fetch 引入的 Request 对象](#fetch-引入的-request-对象)
- [Fetch 引入的 Response 对象](#fetch-引入的-response-对象)
- [Fetch 实例](#fetch-实例)

<!-- /TOC -->

## 概述

Fetch API 是一种新规范，用来取代 XMLHttpRequest 对象。有两个特点，一是接口合理化，Ajax 是将所有不同性质的接口都放在 XHR 对象上，而 Fetch 是将它们分散在几个不同的对象上，设计更合理。二是 Fetch 操作返回 Promise 对象，避免了嵌套的回调函数。同时据传送是以数据流（stream）的形式进行的，对于大文件，数据是一段一段得到的。

### 检测浏览器是否部署

```js
if ('fetch' in window) {
  // 支持
} else {
  // 不支持
}
```

### 简单示例

```js
var option = {
  method: 'POST',
  body: JSON.stringify(data),
  headers: {
    'Content-Type': 'application/json',
  },
  mode: 'cors',
  cache: 'reload',
  credentials: 'same-origin',
};

fetch(url, option)
  .then(function(response) {
    console.log(response.ok); //=> True if status is HTTP 2xx
    console.log(response.status); //=> number 100–599
    console.log(response.statusText); //=> String
    console.log(response.headers); //=> Headers
    console.log(response.url); //=> String
    console.log(response.type); //=> basic(同域)  cors(跨域)  opaque(非cors的跨域)
    console.log(response.headers.get('Content-Type'));

    return response.json();
  })
  .then(function(jsonData) {
    console.log(jsonData);
  })
  .catch(function(error) {
    console.log('error.message');
  });
```

```js
// 上述示例用 xhr 方式
var xhr = new XMLHttpRequest();
xhr.open('POST', url, true);
xhr.reponseType = 'json';
xhr.onload = function() {
  console.log(xhr.reponse);
};
xhr.onerror = function() {
  console.log('出错了');
};
xhr.send();
```

## stream 数据流

数据传送是以数据流（stream）的形式进行的。对于大文件，数据是一段一段得到的。

```js
reponse.text().then(function(reponseText) {
  console.log(reponseText);
});
```

Fetch API 提供以下五个数据流读取器。

| 数据流         | 描述                   |
| -------------- | ---------------------- |
| .text()        | 返回字符串             |
| .json()        | 返回一个 JSON 对象     |
| .blob()        | 返回一个 blob 对象     |
| .formData()    | 返回一个 FormData 对象 |
| .arrayBuffer() | 返回一个二进制数组     |

数据流只能读取一次，一旦读取，数据流就空了。再次读取就不会得到结果。解决方法是在读取之前，先使用.clone()方法，复制一份一模一样的副本。

```js
fetch('flowers.jpg').then(function(response) {
  // 复制一份响应
  // 另外2个静态方法： Response.error()  Response.redirect()
  var response2 = response.clone();

  response.blob().then(function(myBlob) {
    var objectURL = URL.createObjectURL(myBlob);
    image1.src = objectURL;
  });

  response2.blob().then(function(myBlob) {
    var objectURL = URL.createObjectURL(myBlob);
    image2.src = objectURL;
  });
});
```

## Fetch 引入的 Headers 对象

> Headers 对象用来构造/读取 HTTP 数据包的头信息。

```js
var headers = new Headers({
  'Content-Type': 'text/plain',
  'Content-Length': content.length.toString(),
  'X-Custom-Header': 'ProcessThisImmediately',
});
headers.append('Accept', 'application/json');
```

```
// 实例方法
.has(name)    (boolean)
.get(name)    (String)
.set(name, value)
.append(name, value)
.delete(name)
.forEach(function(value, name){ ... }, [thisContext])
```

## Fetch 引入的 Request 对象

> Request 对象用来构造 HTTP 请求

```js
var req = new Request(URL, {
  method: 'POST',
  headers: headers,
  body: JSON.stringify(data),
  mode: 'no-cors', // same-origin  cors
  cache: 'reload',
  credentials: 'same-origin',
});

// 可以在其他实例基础上生成新的 req
var postReq = new Request(req, {method: 'POST'});
```

Request 对象实例的 mode 属性，用来设置是否跨域，合法的值有以下三种：same-origin、no-cors（默认值）、cors。如果 mode 属性为 no-cors，就与默认的浏览器行为没有不同，类似 script 标签加载外部脚本文件、img 标签加载外部图片。当设置为 same-origin 时，只能向同域的 URL 发出请求，否则会报错。如果 mode 属性为 cors，就可以向部署了 CORS 机制的服务器，发出跨域请求。

## Fetch 引入的 Response 对象

> fetch 方法返回 Response 对象实例

| 属性方法                       | 描述                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| status                         | 整数值，表示状态码（比如 200）                               |
| statusText                     | 字符串，表示状态信息，默认是“OK”                             |
| ok                             | 布尔值，表示状态码是否在 200-299 的范围内                    |
| headers                        | Headers 对象，表示 HTTP 回应的头信息                         |
| url                            | 字符串，表示 HTTP 请求的网址                                 |
| type                           | 字符串，合法的值有五个 basic、cors、default、error、opaque。 |
| Response.error()               | 返回一个 type 属性为 error 的 Response 对象实例              |
| Response.redirect(url, status) | 返回的 Response 对象实例会重定向到另一个 URL                 |

basic 表示正常的同域请求；
cors 表示 CORS 机制的跨域请求；
opaque 表示非 CORS 机制的跨域请求，受到严格限制。

error 表示网络出错，无法取得信息，status 属性为 0，headers 属性为空，并且导致 fetch 函数返回 Promise 对象被拒绝。

## Fetch 实例

1. 展示图片

```js
var thumbs = document.querySelectorAll('.thumb');
var mainImg = document.querySelector('.mainImg');

function retrieveImage(requestObj, imageNum) {
  function displayImage(imageNum, blob) {
    var objectURL = URL.createObjectURL(blob);
    thumbs[imageNum].src = objectURL;
    thumbs[imageNum].onclick = function() {
      mainImg.src = objectURL;
      mainImg.className = 'blowup';
      thumbs.forEach(e => (e.className = 'thumb darken'));
    };
  }

  if ('fetch' in window) {
    fetch(requestObj)
      .then(reponse => reponse.blob())
      .then(blob => displayImage(imageNum, blob));
  } else {
    var xhr = new XMLHttpRequest();
    xhr.open('GET', requestObj, true);
    xhr.responseType = 'blob';
    xhr.send();
    xhr.onload = displayImage(imageNum, xhr.response);
  }
}

for (var i = 1, j = thumbs.length; i <= j; i++) {
  var requestObj = '../img/fetch/pic' + i + '.jpg';
  retrieveImage(requestObj, i - 1);
}

mainImg.onclick = function() {
  mainImg.className = 'main';
  thumbs.forEach(e => (e.className = 'thumb'));
};
```

1. 大文件加载

```js
var url = 'LargeFile.txt';
var content = document.getElementById('content');
var progress = document.getElementById('progress');

fetch(url)
  .then(function(response) {
    // 本次请求总的数据长度
    var contentLength = response.headers.get('Content-Length');
    var getStream = function(reader) {
      return reader.read().then(function(result) {
        // 如果数据已经读取完毕，直接返回
        if (result.done) {
          return;
        }
        // 取出本段数据（二进制格式）
        var chunk = result.value;
        var text = '';
        // 假定数据是UTF-8编码，前三字节是数据头，
        // 而且每个字符占据一个字节（即都为英文字符）
        for (var i = 3; i < chunk.byteLength; i++) {
          text += String.fromCharCode(chunk[i]);
        }
        // 将本段数据追加到网页之中
        content.innerHTML += text;
        // 计算当前进度
        progress += chunk.byteLength;
        console.log((progress / contentLength) * 100 + '%');
        // 递归处理下一段数据
        return getStream(reader);
      });
    };
    return getStream(response.body.getReader());
  })
  .catch(function(error) {
    console.log(error);
  });
```
