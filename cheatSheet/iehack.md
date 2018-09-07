# CSS Hack

## IE 条件注释法

```
<!--[if lte IE 8]>
HTML代码块
<![endif]-->
```

## 属性前缀 Hack

```
!important    除了IE6
-/_           IE6
*/+           IE6/IE7
\0            IE8/IE9/IE10
\9\0          IE9/IE10
\0\9          IE8/IE9
\9            IE6/IE7/IE8/IE9/IE10
```

```
color:  blue;                 /*    For latest Firefox, chrome, Safari         */
color:  red\9;                /*    For IE                                     */
color:  yellow\0;             /*    For IE8+                                   */
+color: pink;                 /*    For IE7                                    */
_color: orange;               /*    For IE6                                    */
:root 选择符 {属性\9;}         /*    For IE9                                    */
```

## 选择器前缀 Hack

```
*html  .class{ }                                                 * 前缀只对IE6生效
*+html .class{ }                                                 *+ 前缀只对IE7生效
@media screen\9{...}                                             只对IE6/7生效
@media \0screen {body { background: red; }}                      只对IE8有效
@media \0screen\,screen\9{body { background: blue; }}            只对IE6/7/8有效
@media screen\0 {body { background: green; }}                    只对IE8/9/10有效
@media screen and (min-width:0\0) {body { background: gray; }}   只对IE9/10有效
@media screen and (-ms-high-contrast: active), (-ms-high-contrast: none) {body { background: orange; }}      只对IE10有效
```

## IE10 在使用伪元素动画有一个问题: 需要使用一个空的:hover 来激活

```
.test:hover {}
.test:hover::before(after) { /* 这时animation和transition才生效 */ }
```
