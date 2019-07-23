
#  buildURL.js
主要知识点：encodeURI和encodeURIComponent

涉及代码片段
```js
function encode(val) {
  return encodeURIComponent(val).
    replace(/%40/gi, '@').
    replace(/%3A/gi, ':').
    replace(/%24/g, '$').
    replace(/%2C/gi, ',').
    replace(/%20/g, '+').
    //Using square bracket notation for URL params
    replace(/%5B/gi, '[').
    replace(/%5D/gi, ']');
}
```
<h2 style="color:#4042b3;font-weight:600">encodeURI</h2>
[encodeURI](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/encodeURI):encodeURI()  函数通过将特定字符的每个实例替换为一个、两个、三或四转义序列来对统一资源标识符 (URI) 进行编码 (该字符的 UTF-8 编码仅为四转义序列,由两个 "代理" 字符组成)。

在URI中，有一些字符会[被%进行编码](https://en.wikipedia.org/wiki/Percent-encoding)，维基百科中的介绍为：百分号编码（英语：Percent-encoding，又称：URL编码（英语：URL encoding）），是特定上下文的统一资源定位符 (URL)的编码机制. 实际上也适用于统一资源标志符（URI）的编码。也用于为application/x-www-form-urlencodedMIME准备数据，因为它用于通过HTTP的请求操作（request）提交HTML表单数据。

encodeURI忽略协议前缀（'http：//'）和域名部分。

不会被编码的字符包括以下：
| 类型        | 包含           |
| ------------- |:-------------:|
| 保留字符      | `; , / ? : @ & = + $` |
| 非转义的字符      | `字母 数字 - _ . ! ~ * ' ( )`     |
| 数字符号 | `#`     |

其他字符都会被%进行编码，例如：`中国`、`ママ`等非以上列举的字符。

值得一提的是，URI的规范现遵循较新的标准[RFC3986](https://tools.ietf.org/html/rfc3986)，包含的保留字符又增加了：
>`!	*	'	(	)	;	:	@	&	=	+	$	,	/	?	#	[	]`

新增的 ` [ ` 和 ` ] ` 是为IPv6而被保留的。因此，除了上述字符以外，URI中的其它字符必须用百分号编码。
<table cellpadding="6px" border="1" style="border:1px solid #111; border-collapse:collapse; background-color:white;">
<caption>编码对应值
</caption>
<thead><tr>
<th><code style="color:#7c7">!</code></th>
<th><code style="color:#7c7">#</code></th>
<th><code style="color:#7c7">$</code></th>
<th><code style="color:#7c7">%</code></th>
<th><code style="color:#7c7">&amp;</code></th>
<th><code style="color:#7c7">'</code></th>
<th><code style="color:#7c7">(</code></th>
<th><code style="color:#7c7">)</code></th>
<th><code style="color:#7c7">*</code></th>
<th><code style="color:#7c7">+</code></th>
<th><code style="color:#7c7">,</code></th>
<th><code style="color:#7c7">/</code></th>
<th><code style="color:#7c7">:</code></th>
<th><code style="color:#7c7">;</code></th>
<th><code style="color:#7c7">=</code></th>
<th><code style="color:#7c7">?</code></th>
<th><code style="color:#7c7">@</code></th>
<th><code style="color:#7c7">[</code></th>
<th><code style="color:#7c7">]</code></th>
</tr>
</thead>
<tbody>
<tr>
<td><code>%21</code></td>
<td><code>%23</code></td>
<td><code>%24</code></td>
<td><code>%25</code></td>
<td><code>%26</code></td>
<td><code>%27</code></td>
<td><code>%28</code></td>
<td><code>%29</code></td>
<td><code>%2A</code></td>
<td><code>%2B</code></td>
<td><code>%2C</code></td>
<td><code>%2F</code></td>
<td><code>%3A</code></td>
<td><code>%3B</code></td>
<td><code>%3D</code></td>
<td><code>%3F</code></td>
<td><code>%40</code></td>
<td><code>%5B</code></td>
<td><code>%5D</code>
</td></tr></tbody>
</table>

<h2 style="color:#4042b3;font-weight:600">encodeURIComponent</h2>
encodeURIComponent()是对统一资源标识符（URI）的组成部分进行编码的方法。它使用一到四个转义序列来表示字符串中的每个字符的UTF-8编码（只有由两个Unicode代理区字符组成的字符才用四个转义字符编码）。

乍一看是不是和encodeURI没啥区别呢~，但是这个API强调的是：“对URI组成部分进行编码的方法”，这个方法会转义除了字母、数字`(` `)` `.` `!` `~` `*` `'` `-`和_之外的所有字符。包含上述介绍中的一些encodeURI不会进行转义的字符。
具体说这个两个方法之前有个结论：
1. encodeURIComponent()适合用来编码我们的URI的queryString部分，然后拼接到URI的其他部分。
2. 如果使用encodeURIComponent()来对URI进行编码，那么它将不会用作一个有效URI
3. 编码一个有效URI，请使用encodeURI
4. 根据上述讲述，encodeURI不编码queryString或哈希值(#)，因此，需要编码除协议和域名外的部分，使用如果使用encodeURIComponent()

<h2 style="color:#4042b3;font-weight:600">使用</h2>
其实，我们的常见问题是特殊字符和空格，有些时候无法避免在queryString中带有一些特殊字符（中文就是一种……）,这个时候我们就需要使用encodeURIComponent()来转义queryString，然后与URL的其他部分拼接起来。但是鉴于大多数浏览器会自动将空格等字符转换为％编码的URL编码值，但始终在一小部分浏览器中存在异常（IE会出现异常，重现率100%）。幸运的是，浏览器提供本机URL编码和解码功能：encodeURI，encodeURIComponent。那么，相应的，decodeURI，decodeURIComponent就是用来解码已经编码的内容的方法。
