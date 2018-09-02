# html 标签速记

## 整体结构

```html
<!DOCTYPE html>
<html>
    <head>
    </head>

    <body>
    </body>
</html>
```

## head 部分

head 标签，是存放文档的元信息部分，此标签下的内容不会直接呈现在网页中。

| 子标签 | 属性值                                                                  |
| ------ | ----------------------------------------------------------------------- |
| base   | href target                                                             |
| link   | rel type href hreflang media sizes crossorigin integrity referrerpolicy |
| style  | type media title                                                        |
| title  |
| meta   | charset content http-equiv name                                         |

## body 部分

body 标签下的子标签，是呈现在网页端的内容，按照类别，主要分别以下几大类：

| 类别           | 包含的子标签                                                                                                                                                                                                          |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 文档结构       | header hgroup main footer aside nav article section address                                                                                                                                                           |
| 文本结构       | h1-h6 p a br hr div span details summary                                                                                                                                                                              |
| 文本样式       | i b em strong sup sub del ins mark q blockquote pre abbr time cite                                                                                                                                                    |
| 不常用文本样式 | bdo dfn var code samp kbd wbr ruby rb rt rp rtc                                                                                                                                                                       |
| 列表           | ol ul li dl dt dd                                                                                                                                                                                                     |
| 表格           | table caption thead tbody tfoot tr th td col colgroup                                                                                                                                                                 |
| 表单           | form fieldset legend label input datalist select optgroup option textarea button output meter progress                                                                                                                |
| 图片           | img figure figcaption map area                                                                                                                                                                                        |
| 音频 、视频    | audio source video track                                                                                                                                                                                              |
| 内嵌           | object param embed script noscript                                                                                                                                                                                    |
| 框架           | iframe noframes                                                                                                                                                                                                       |
| 画布           | canvas                                                                                                                                                                                                                |
| 实验标签       | data dialog menu menuitem picture template slot                                                                                                                                                                       |
| 废弃标签       | acronym applet basefont bdi bgsound big blink center command content dir element font frame frameset image isindex keygen listing marquee multicol nextid nobr noembed plaintexts shadow small spacer strike tt u xmp |

## input 标签 不同 type 拥有的不同属性

| type 类型                          | 拥有属性                                                             |
| ---------------------------------- | -------------------------------------------------------------------- |
| 通用                               | name required autocomplete autofocus disabled form                   |
| text password email search tel url | size placeholder list maxlength minlength pattern readonly inputmode |
| raido checkbox                     | checked                                                              |
| number range date time             | max min step                                                         |
| file                               | accept capture multiple                                              |
| image                              | src width height                                                     |
| submit_button image                | formaction formmethod formenctype formtarget formnovalidate          |

## 自闭标签

| 标签     | 作用                                                                                                                                                                                      |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| area     | 标签定义图像映射中的区域(注:图像映射指得是带有可点击区域的图像)。area 元素总是嵌套在 `<map>` 标签中。                                                                                     |
| base     | 标签为页面上的所有链接规定的默认地址或默认目标。                                                                                                                                          |
| br       | 表示换行。                                                                                                                                                                                |
| col      | 为表格中一个或多个列定义属性值。                                                                                                                                                          |
| colgroup | 当有`span`属性值时。                                                                                                                                                                      |
| command  | 元素表示用户能够调用的命令。标签可以定义命令按钮，比如单选按钮、复选框或按钮。只有当 command 元素位于 menu 元素内时，该元素才是可见的。否则不会显示这个元素，但是可以用它规定键盘快捷键。 |
| embed    | 可以在页面中嵌入任何类型的文档。                                                                                                                                                          |
| hr       | 表示画一条线。                                                                                                                                                                            |
| img      | 放入图片。                                                                                                                                                                                |
| input    | 用户输入的标签。                                                                                                                                                                          |
| keygen   | 标签规定用于表单的密钥对生成器字段。当提交表单时，私钥存储在本地，公钥发送到服务器。                                                                                                      |
| link     | 标签定义文档与外部资源的关系。 标签最常见的用途是链接样式表。                                                                                                                             |
| meta     | 元素可提供有关页面的元信息（meta-information），比如针对搜索引擎和更新频度的描述和关键词。标签位于文档的头部，不包含任何内容。`<meta>`标签的属性定义了与文档相关联的名称/值对。           |
| param    | param 元素允许您为插入 XHTML 文档的对象规定 run-time 设置，也就是说，此标签可为包含它的 `<object>` 或者 `<applet>` 标签提供参数。                                                         |
| source   | 为媒介元素，定义媒介资源。                                                                                                                                                                |
| track    | 标签为诸如 video 元素之类的媒介规定外部文本轨道。用于规定字幕文件或其他包含文本的文件，当媒介播放时，这些文件是可见的。                                                                   |
| wbr      | 规定在文本中的何处适合添加换行符。如果单词太长，或者您担心浏览器会在错误的位置换行，那么您可以使用 `<wbr>` 元素来添加 Word Break Opportunity（单词换行时机）。                            |
