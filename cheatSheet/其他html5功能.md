# 其他 html5 功能

---

<!-- TOC depthFrom:2 -->

- [跨文档消息传送（XDM）](#跨文档消息传送xdm)
- [拖放事件](#拖放事件)
- [历史状态管理](#历史状态管理)

<!-- /TOC -->

## 跨文档消息传送（XDM）

> cross-document messaging

1. 向页面中的 `<iframe>` 传送消息，或者由当前页面弹出的窗口。
1. XDM 的核心是 postMessage 方法，接收两个参数，一条消息和一个表示消息接收方来自哪个域的字符串。

   ```js
   let iframeWindow = document.getElementById('myframe').contentWindow;
   iframeWindow.postMessage('A secret', 'http://www.wrox.com');
   ```

1. 如果 postMessage 的第二个参数是'\*'，则可以把消息发送给来自任何域的文档，但是我们不推荐这样做。
1. 接收到 XDM 消息的时候会触发 window 对象的 message 事件，这个事件是以异步形式触发的，因此从发送消息到接收消息（触发窗口的 message 事件）可能要经过一段时间的延迟。触发 message 事件之后，传递给 onmessage 处理程序的事件包含以下三方面的信息。
   1. data：作为 postMessage 第一个参数传入的字符串参数
   1. origin：发送消息的文档所在的域，例如"http://www.wrox.com"
   1. source：发送消息的文档的 window 对象的代理，这个代理对象主要用于发送一条消息的窗口中调用 postMessage 方法，如果发送消息的窗口来自同一个域，那么这个对象就是 window。
1. 接收到消息后，需要验证发送窗口的来源。在 onmessage 事件上检测。比如：

   ```js
   window.addEventListener(
     'message',
     function(event) {
       if (event.origin === 'http://www.wrox.com') {
         processMessage(event.data); // 处理接收到的数据
         event.source.postMessage('Received!', 'http://p2p.wrox.com'); // 发送回执
       }
     },
     false
   );
   ```

## 拖放事件

1. 节点属性设为可拖动 `draggable="true"`
   1. 可拖动元素依次触发事件为
   1. dragstart
   1. drag
   1. dragend
1. 节点属性设为可放置 `<div ondragover="event.preventDefault()">`
   1. 可放置元素依次触发事件为
   1. dragenter
   1. dragover
   1. dragleave / drop
1. 事件处理中，用 `dataTransfer` 对象传递数据

   1. getData() 方法，在 `drop` 事件中读取数据
   1. setData() 方法，可以在 `dragstart` 事件中调用，手动保存要传输的数据
   1. types 属性，当前保存的数据类型
   1. files 属性， `event.dataTransfer.files` ，涉及文件时才有数据。

      ```js
      div.addEventListener(
        'drop',
        function(e) {
          e.preventDefault();
          e.stopPropagation();

          var fileList = e.dataTransfer.files;
          if (fileList.length > 0) {
            var file = fileList[0];
            var reader = new FileReader();
            reader.onloadend = function(e) {
              if (e.target.readyState == FileReader.DONE) {
                var content = reader.result;
                contentDiv.innerHTML = 'File: ' + file.name + '\n\n' + content;
              }
            };
            reader.readAsBinaryString(file);
          }
        },
        false
      );
      ```

## 历史状态管理

> 不必卸载当前页面，即可修改浏览器的历史状态栈

1. history.pushState({state object}, title. hashURL);
1. history.replaceState({state object}, title. hashURL);
