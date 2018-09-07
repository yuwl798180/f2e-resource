# Ajax

---

<!-- TOC depthFrom:2 depthTo:2 -->

- [含义](#含义)
- [XMLHttpRequest 实例的属性](#xmlhttprequest-实例的属性)
- [XMLHttpRequest 实例的方法](#xmlhttprequest-实例的方法)
- [XMLHttpRequest 实例的事件](#xmlhttprequest-实例的事件)
- [文件上传](#文件上传)

<!-- /TOC -->

## 含义

> Asynchronous JavaScript XML

浏览器与服务器之间，采用 HTTP 协议通信。Ajax 通过原生的 XMLHttpRequest 对象发出 HTTP 请求，得到服务器返回的数据后，再进行处理。Ajax 是脚本发起 HTTP 通信的代名词。

Ajax 只能向同源网址（协议、域名、端口都相同）发出 HTTP 请求。

具体来说，Ajax 包括以下几个步骤。

1. 创建 Ajax 对象
   - `var xhr = new XMLHttpRequest();`
1. 发出 HTTP 请求
   - `xhr.open('GET', 'http://www.example.com/page.php', true);`
   - `xhr.send(null);`
1. 接收服务器传回的数据
   - `xhr.onreadystatechange = handleStateChangeFunction;`
1. 更新网页数据

   ```js
   handleStateChangeFunction = function() {...}

   xhr.onreadystatechange = function() {
       if (xhr.readyState === 4) {
           if ( (xhr.status >= 200 && xhr.status <300) ||
                (xhr.status ===304 ) ) {
               console.log(xhr.responseText);
           } else {
               console.error(xhr.statusText);
           }
       }
   };

   xhr.onerror = function() {
       console.error(xhr.statusText);
   };

   xhr.timeout = 2000;
   xhr.ontimeout = function() {
       console.error("The request for " + url + " timed out.");
   };
   ```

### xhr 实例属性

| xhr 实例属性       | 描述                                                                |
| ------------------ | ------------------------------------------------------------------- |
| readyState         | 返回整数表示 xhr 请求状态（只读）                                   |
| onreadystatechange | 指向回调函数，当请求状态变化时调用函数                              |
| response           | 返回接收到的数据体(body 部分)，类型由 xhr.responseType 指定（只读） |
| responseType       | 指定服务器返回数据的类型                                            |
| responseText       | 返回从服务器接收到的字符串（只读）                                  |
| responseXML        | 返回从服务器接收到的 Document 对象（只读）                          |
| status             | 返回请求得到的 HTTP 状态码（只读）                                  |
| statusText         | 返回请求得到的 HTTP 状态信息（只读）                                |
| timeout            | 设置请求最大容忍时间，单位毫秒                                      |
| 事件监听接口       | xhr2 的实例除了 `onreadystatechange` 添加了更多事件                 |
| withCredentials    | 跨域请求时，是否发送 Cookie，默认 false                             |

### xhr 实例方法

| xhr 实例方法                             | 描述                                                 |
| ---------------------------------------- | ---------------------------------------------------- |
| abort()                                  | 终止已经发出的 HTTP 请求                             |
| getAllResponseHeaders()                  | 返回服务器发来的所有 HTTP 头信息                     |
| getResponseHeader(name)                  | 返回 HTTP 头信息指定字段的值                         |
| open(method, url, async, user, password) | 发送 HTTP 请求的参数                                 |
| send(body)                               | 可选参数作为请求主体，GET 请求设置为 null            |
| setRequestHeader(header, value)          | 设置 HTTP 头信息，必须在 open 之后、send 之前调用    |
| overrideMimeType(DOMString mimetype)     | 设置服务器返回数据的 MIME 类型，必须在 send 之前调用 |

### xhr 实例事件

| xhr 实例事件          | 描述                                              |
| --------------------- | ------------------------------------------------- |
| readyStateChange 事件 | 请求状态改变时，xhr1 定义的唯一事件               |
| abort 事件            | 请求被中止时调用                                  |
| error 事件            | 请求发生错误                                      |
| load 事件             | 请求成功完成                                      |
| loadstart 事件        | 请求开始加载数据                                  |
| progress 事件         | 间歇调用该方法用来获取请求过程中的信息            |
| timeout 事件          | 时间超时时调用，只有设置 timeout 属性超时才会调用 |
| loadend 事件          | 请求完成，不管成果或失败                          |

## XMLHttpRequest 实例的属性

### readyState

只读属性，用一个整数和对应的常量，表示 XMLHttpRequest 请求当前所处的状态。在通信过程中，每当发生状态变化的时候，readyState 属性的值就会发生改变。这个值每一次变化，都会触发 readyStateChange 事件。

- 0，对应常量 UNSENT，表示 XMLHttpRequest 实例已经生成，但是 open()方法还没有被调用。
- 1，对应常量 OPENED，表示 send()方法还没有被调用，仍然可以使用 setRequestHeader()，设定 HTTP 请求的头信息。
- 2，对应常量 HEADERS_RECEIVED，表示 send()方法已经执行，并且头信息和状态码已经收到。
- 3，对应常量 LOADING，表示正在接收服务器传来的 body 部分的数据，如果 responseType 属性是 text 或者空字符串，responseText 就会包含已经收到的部分信息。
- 4，对应常量 DONE，表示服务器数据已经完全接收，或者本次接收已经失败了。

### onreadystatechange

指向一个回调函数，当 readystatechange 事件发生的时候，这个回调函数就会调用，并且实例的 readyState 属性也会发生变化。

### response

属性为只读，返回接收到的数据体。类型可以是 ArrayBuffer、Blob、Document、JSON 对象、或者一个字符串，这由实例的 responseType 属性的值决定。

### responseType

用来指定服务器返回数据（xhr.response）的类型。有： `text` （字符串）`json` （JSON 对象）`document` （Document 对象，比如 XML 文档）`blob` （Blob 对象，二进制数据，比如图片）`arraybuffer` （按照数组方式处理二进制数据）等类型。若设置为空字符串，也是默认值，等效于 `text`。

### responseText

属性返回从服务器接收到的字符串，该属性为只读。如果本次请求没有成功或者数据不完整，该属性就会等于 null。如果服务器返回的数据格式是 JSON，就可以使用 responseText 属性
`const data = JSON.parse(xhr.reponseText);`

### responseXML

属性返回从服务器接收到的 Document 对象，该属性为只读。如果本次请求没有成功，或者数据不完整，或者不能被解析为 XML 或 HTML，该属性等于 null。返回的数据会被直接解析为 DOM 对象。如果服务器返回的数据，没有明示 Content-Type 头信息等于 text/xml，可以使用 overrideMimeType()方法，指定 XMLHttpRequest 对象将返回的数据解析为 XML。

### status

属性为只读属性，表示本次请求所得到的 HTTP 状态码，它是一个整数。

| 状态码 | 含义                                  |
| ------ | ------------------------------------- |
| 200    | OK，访问正常                          |
| 301    | Moved Permanently，永久移动           |
| 302    | Move temporarily，暂时移动            |
| 304    | Not Modified，未修改                  |
| 307    | Temporary Redirect，暂时重定向        |
| 401    | Unauthorized，未授权                  |
| 403    | Forbidden，禁止访问                   |
| 404    | Not Found，未发现指定网址             |
| 500    | Internal Server Error，服务器发生错误 |

### statusText

返回一个字符串，表示服务器发送的状态提示。不同于 status 属性，该属性包含整个状态信息，比如 "200 OK"。

### timeout

timeout 属性等于一个整数，表示多少毫秒后，如果请求仍然没有得到结果，就会自动终止。如果该属性等于 0，就表示没有时间限制。

### 事件监听接口

XMLHttpRequest 第一版，只能对 onreadystatechange 这一个事件指定回调函数。该事件对所有情况作出响应。
XMLHttpRequest 第二版允许对更多的事件指定回调函数。如果发生网络错误（比如服务器无法连通），onerror 事件无法获取报错信息，所以只能显示报错。

| 事件        | 描述                                   |
| ----------- | -------------------------------------- |
| onloadstart | 请求发出                               |
| onprogress  | 正在发送和加载数据                     |
| onabort     | 请求被中止，比如用户调用了 abort()方法 |
| onerror     | 请求失败                               |
| onload      | 请求成功完成                           |
| ontimeout   | 用户指定的时限到期，请求还未完成       |
| onloadend   | 请求完成，不管成果或失败               |

### withCredentials

跨域请求时，用户信息（比如 Cookie 和认证的 HTTP 头信息）是否会包含在请求之中，默认为 false。即向 example.com 发出跨域请求时，不会发送 example.com 设置在本机上的 Cookie（如果有的话）。

`xhr.withCredentials = true;`

为了让这个属性生效，服务器必须显式返回 `Access-Control-Allow-Credentials` 这个头信息。

属性打开的话，不仅会发送 Cookie，还会设置远程主机指定的 Cookie。注意，此时你的脚本还是遵守同源政策，无法 从 `document.cookie` 或者 HTTP 回应的头信息之中，读取这些 Cookie。

## XMLHttpRequest 实例的方法

### abort()

```js
ajax.abort();
ajax = null;
```

### getAllResponseHeaders()

返回服务器发来的所有 HTTP 头信息，格式为字符串，每个头信息之间使用 CRLF 分隔。如果没有受到服务器回应，该属性返回 null。

### getResponseHeader()

```js
let getHeaderTime = ajax.getResponHeader('Last-Modified');
```

### open()

第三个参数为是否为异步，默认为 true。如果对使用过 open()方法的请求，再次使用这个方法，等同于调用 abort()。

### send()

如果不带参数，就表示 HTTP 请求只包含头信息，也就是只有一个 URL，典型例子就是 GET 请求。如果带有参数，就表示除了头信息，还带有包含具体数据的信息体，典型例子就是 POST 请求。如果请求是异步的（默认为异步），该方法在发出请求后会立即返回。如果请求为同步，该方法只有等到收到服务器回应后，才会返回。所有 XMLHttpRequest 的监听事件，都必须在 send()方法调用之前设定。

```js
let xhr = new XMLHttpRequest();
let data =
  'email=' +
  encodeURIComponent(email) +
  '&password=' +
  encodeURIComponent(password);
ajax.open('POST', 'http://www.example.com/somepage.php', true);
ajax.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.onload = function() {};
ajax.send(data);
```

send 方法的参数就是发送的数据。多种格式的数据，都可以作为它的参数。

```js
send();
send(String data);
send(ArrayBufferView data);  // 发送二进制数据
send(Blob data);             // 发送二进制数据
send(Document data);  // 在发送之前，数据会先被串行化
send(FormData data);  // FormData类型可以用于构造表单数据
```

```js
var formElement = document.querySelector('form');
var formData = new FormData(formElement);

// 添加自定义表单数据
// formData.append(name, file, filename);
// formData.append(name, blob, filename);
// formData.append(name, value);
formData.append('csrf', 'e6c');

// 添加JavaScript生成的文件
var content = '<a id="a"><b id="b">hey!</b></a>';
var blob = new Blob([content], {type: 'text/xml'});
formData.append('webmasterfile', blob);

var request = new XMLHttpRequest();
request.open('POST', 'submitform.php', true);
xhr.onload = function() {};
request.send(formData);
```

### setRequestHeader()

用于设置 HTTP 头信息，该方法必须在 open()之后、send()之前调用。如果该方法多次调用，设定同一个字段，则每一次调用的值会被合并成一个单一的值发送。

`xhr.setRequestHeader('Content-Type', 'application/json');`

### overrideMimeType()

该方法用来指定服务器返回数据的 MIME 类型。该方法必须在 send()之前调用。

传统上，如果希望从服务器取回二进制数据，就要使用这个方法，人为的将数据类型伪装成文本数据。在 XMLHttpRequest 版本升级以后，就不再采用这种方式，一般采用指定 responseType 的方法。

`xhr.responseType = "arraybuffer";`

## XMLHttpRequest 实例的事件

### progress 事件

上传文件时，XMLHTTPRequest 对象的 upload 属性有一个 progress，会不断返回上传的进度。文件上传时，对 upload 属性指定 progress 事件回调函数，即可获得上传的进度。

```js
// html 部分
// <progress min="0" max="100" value="0">0% complete</progress>

function upload(blobOrFile) {
    var xhr = new XMLHttpRequest();
    xhr.open('POST', '/server', true);
    xhr.onload = function(e) { ... };

    // Listen to the upload progress.
    var progressBar = document.querySelector('progress');
    xhr.upload.onprogress = function(e) {
        if(e.lengthComputable) {
            progressBar.value = (e.loaded / e.total) * 100;
            progressBar.textContent = progressBar.value;
        }
    };

    xhr.send(blobOrFile);
}

upload(new Blob(['hello world'], {type: 'text/plain'}));
```

### loadend 事件

abort、load 和 error 这三个事件，会伴随一个 loadend 事件，表示请求结束，但不知道其是否成功。

## 文件上传

### `<form>` 表单上传格式

HTML 网页的 `<form>` 元素能够以四种格式，向服务器发送数据。

1. 使用 GET 方法，enctype 属性将被忽略。

   ```
   ?foo=bar&baz=The%20first%20line.%0AThe%20second%20line.
   ```

1. 使用 POST 方法，将 enctype 属性设为 `application/x-www-form-urlencoded`，这是默认方法。

   ```
   Content-Type: application/x-www-form-urlencoded
   // 在发送前编码所有字符
   foo=bar&baz=The+first+line.&#37;0D%0AThe+second+line.%0D%0A
   ```

1. 使用 POST 方法，将 enctype 属性设为 `text/plain`。

   ```
   Content-Type: text/plain

   foo=bar
   baz=The first line.
   The second line.
   ```

1. 使用 POST 方法，将 enctype 属性设为 `multipart/form-data`。

   ```
   Content-Type: multipart/form-data; boundary=---------------------------314911788813839

   -----------------------------314911788813839
   Content-Disposition: form-data; name="foo"

   bar
   -----------------------------314911788813839
   Content-Disposition: form-data; name="baz"

   The first line.
   The second line.

   -----------------------------314911788813839--
   ```

### `FormData` 模拟文件上传

```js
var fileSelect = document.querySelector('input[type="file"]');
var files = fileSelect.files;
var formData = new FormData();
for (var i = 0, j = files.length; i < j; i++) {
  var file = files[i];
  if (!file.type.match('image.*')) {
    continue;
  }
  formData.append('photos[]', file, file.name);
}

var xhr = new XMLHttpRequest();
xhr.open('POST', 'handler.php', true);
xhr.onload = function() {
  if (xhr.status !== 200) {
    console.log('An error occured!');
  }
};
xhr.send(formData);
```

### `File API` 直接上传

```js
var file = document.getElementById('test-input').files[0];

var xhr = new XMLHttpRequest();
xhr.open('POST', 'myserver/uploads', true);
xhr.setRequestHeader('Content-Type', 'file.type');
xhr.send(file);
```
