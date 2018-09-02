> 浏览器对象模型：Browser Object Document

<!-- TOC depthTo:2 -->

- [Window 对象](#window-对象)
- [Location 对象](#location-对象)
- [Navigator 对象属性](#navigator-对象属性)
- [History 对象](#history-对象)
- [Screen 对象](#screen-对象)

<!-- /TOC -->

## Window 对象

1. 页面视口大小、整个网页大小、网页被卷起来的大小

   ```js
   var viewportWidth = window.innerWidth; // 视口大小
   var viewportHeight = window.innerHeight;

   var HTMLwidth = document.documentElement.scrollWidth; // 网页大小
   var HTMLHeight = document.documentElement.scrollHeight;

   var scrollTop = document.body.scrollTop; // 网页上方被卷起的部分
   var scrollLeft = document.body.scrollLeft;

   if (typeof viewportWidth !== 'number') {
     if (document.compatMode === 'CSS1Compat') {
       // 标准模式
       viewportWidth = document.documentElement.clientWidth;
       viewportHeight = document.documentElement.clientHeight;
     } else {
       // 兼容模式
       viewportWidth = document.body.clientWidth;
       viewportHeight = document.body.clientHeight;
     }
   }
   ```

1. 显示器屏幕大小、用户实际工作区大小、窗口位置

   ```js
   var screenWidth = window.screen.width; // 显示器屏幕大小
   var screenHeight = window.screen.height;

   var workWidth = window.screen.availWidth; // 实际工作区（减去任务栏等非工作区模块大小）
   var workHeight = window.screen.availHeight;

   var topPos = window.screenTop; // 浏览器窗口距离屏幕上端距离
   var leftPos = window.screenLeft;

   if (typeof topPos !== 'number') {
     topPos = window.screenY;
   }
   if (typeof leftPos !== 'number') {
     leftPos = window.screenX;
   }
   ```

1. setTimeout / clearTimeout / setInterval / clearInterval

   ```js
   // 第一个参数最好不用 code
   var timeoutID = scope.setTimeout(function[, delay, param1, param2, ...]);

   var intervalID = setTimeout(() => { myFunc('one', 'two'); }, 1000);
   ```

1. 其他方法

   | 方法                        | 描述                                           |
   | --------------------------- | ---------------------------------------------- |
   | alert(message)              | 显示带有一段消息和一个确认按钮的警告框         |
   | confirm(message)            | 显示带有一段消息以及确认按钮和取消按钮的对话框 |
   | prompt(message, default)    | 显示可提示用户输入的对话框                     |
   | open(url, name, [features]) | 打开一个新的浏览器窗口或查找一个已命名的窗口   |
   | close()                     | 关闭浏览器窗口                                 |
   | print()                     | 打印当前窗口的内容                             |
   | moveBy(x,y)                 | 可相对窗口的当前坐标把它移动指定的像素         |
   | moveTo(x,y)                 | 把窗口的左上角移动到一个指定的坐标             |
   | resizeBy(x,y)               | 按照指定的像素调整窗口的大小                   |
   | resizeTo(x,y)               | 把窗口的大小调整到指定的宽度和高度             |
   | scrollBy(x,y)               | 按照指定的像素值来滚动内容                     |
   | scrollTo(x,y)               | 把内容滚动到指定的坐标                         |

## Location 对象

1. 属性

| 属性     | 描述                                        |
| -------- | ------------------------------------------- |
| hash     | 设置或返回从井号 (#) 开始的 URL（锚）       |
| host     | 设置或返回主机名和当前 URL 的端口号         |
| hostname | 设置或返回当前 URL 的主机名                 |
| href     | 设置或返回完整的 URL                        |
| pathname | 设置或返回当前 URL 的路径部分               |
| port     | 设置或返回当前 URL 的端口号                 |
| protocol | 设置或返回当前 URL 的协议                   |
| search   | 设置或返回从问号 (?) 开始的 URL（查询部分） |

1. 方法

| 属性            | 描述                                                 |
| --------------- | ---------------------------------------------------- |
| assign(URL)     | 加载新的文档 / === window.location === location.href |
| replace(newURL) | 用新的文档替换当前文档                               |
| reload(force)   | 重新加载当前文档                                     |

## Navigator 对象属性

| 属性          | 描述                                         |
| ------------- | -------------------------------------------- |
| appCodeName   | 返回浏览器的代码名 (Mozilla)                 |
| appName       | 返回浏览器的名称 (Netscape)                  |
| platform      | 返回运行浏览器的操作系统平台 (Win32)         |
| onLine        | 返回指明系统是否处于脱机模式的布尔值         |
| cookieEnabled | 返回指明浏览器中是否启用 cookie 的布尔值     |
| userAgent     | 返回由客户机发送服务器的 user-agent 头部的值 |
| appVersion    | 返回浏览器的平台和版本信息                   |

## History 对象

| 属性方法                           | 描述                                                                                                                                                                   |
| ---------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| length                             | 返回浏览器历史列表中的 URL 数量                                                                                                                                        |
| back()                             | 加载 history 列表中的前一个 URL                                                                                                                                        |
| forward()                          | 加载 history 列表中的下一个 URL                                                                                                                                        |
| go()                               | 加载 history 列表中的某个具体页面                                                                                                                                      |
| pushState(stateObject, title, URI) | 创建新的条目（会改变 referrer 的值，调用方法后创建的 XMLHttpRequest 对象会在 HTTP 请求头中使用这个值。 referrer 的值则是创建 XMLHttpRequest 对象时所处的窗口的 URL。） |
| history.replaceState()             | 会修改当前历史记录条目而并非创建新的条目                                                                                                                               |
| history.state                      | 该属性返回当前页面的 state 对象                                                                                                                                        |

## Screen 对象

| 属性        | 描述                                                                |
| ----------- | ------------------------------------------------------------------- |
| height      | 返回显示屏幕的高度                                                  |
| width       | 返回显示器屏幕的宽度                                                |
| availHeight | 返回显示屏幕的高度 (除 Windows 任务栏之外)                          |
| availWidth  | 返回显示屏幕的宽度 (除 Windows 任务栏之外)                          |
| colorDepth  | 返回目标设备或缓冲器上的调色板的比特深度 (24)                       |
| pixelDepth  | 返回显示屏幕的颜色分辨率（比特每像素）(24)                          |
| orientation | 提供当前屏幕方向(var orientation = window.screen.orientation.type;) |
