## 浏览器安全机制:同源策略

同源策略控制了不同源之间的交互，例如在使用XMLHttpRequest 或 \<img\> 标签时则会受到同源策略的约束。交互通常分为三类：

* 通常允许进行跨域写操作(Cross-origin writes). 例如链接(links), 重定向以及表单提交. 特定少数的HTTP请求需要添加 preflight.
* 通常允许跨域资源嵌入(Cross-origin embedding).之后下面会举例说明.
* 通常不允许跨域读操作(Cross-origin reads). 但常可以通过内嵌资源来巧妙的进行读取访问。例如可以读取嵌入图片的高度和宽度, 调用内嵌脚本的方法, 或availability of an embedded resource.

#### 如何允许跨源访问
使用 CORS 允许跨源访问.

Cross-Origin Resource Sharing (CORS) A user agent makes a cross-origin HTTP request when it requests a resource from a different domain, protocol, or port than the one from which the current document originated.
An example of a cross-origin request: A HTML page served from http://domain-a.com makes an \<img\> src request for http://domain-b.com/image.jpg. Many pages on the web today load resources like CSS stylesheets, images, and scripts from separate domains, such as content delivery networks (CDNs).

![CORS](https://mdn.mozillademos.org/files/14295/CORS_principle.png)

For security reasons, browsers restrict cross-origin HTTP requests initiated from within scripts. For example, XMLHttpRequest and the Fetch API follow the same-origin policy. This means that a web application using those APIs can only request HTTP resources from the same domain the application was loaded from unless CORS headers are used.
The CORS mechanism supports secure cross-domain requests and data transfers between browsers and web servers. Modern browsers use CORS in an API container such as XMLHttpRequest or Fetch to help mitigate the risks of cross-origin HTTP requests.
<br>
#### 如何阻止跨源访问

* 阻止跨域写操作，只要检测请求中的一个不可测的标记(CSRF token)即可，这个标记被称为Cross-Site Request Forgery (CSRF) 标记。必须使用这个标记来阻止页面的跨站读操作。
* 阻止资源的跨站读取，需要保证该资源是不可嵌入的。阻止嵌入行为是必须的，因为嵌入资源通常向其暴露信息。
* 阻止跨站嵌入，确保你得资源不能是以上列出的可嵌入资源格式。多数情况下浏览器都不会遵守Conten-Type消息头。例如，如果你在 \<script\> 标签中嵌入HTML文档，浏览器仍将HTML解析为Javascript。When your resource is not an entry point to your site, you can also use a CSRF token to prevent embedding.
<br>
<br>
<br>


## XSS 攻击(Cross-site Scripting)

* 反射型XSS
* 存储型XSS
* DOM Based XSS

XSS的本质是一种 "HTML注入", 用户的数据被当成了HTML代码一部分来执行, 从而混淆了原本的语义, 产生了新的语义.
对于XSS攻击来说, JavaScript工作在渲染后的浏览器环境中, 无法控制用户浏览器发出的HTTP头.因为浏览器同源策略的原因, XSS也受到同源策略的限制——发生在A域上的XSS很难影响到B域的用户.

#### XSS的防御
* 浏览器将禁止页面的 JavaScript 访问带有 HttpOnly 属性的 Cookie, HttpOnly 解决 XSS 后的 Cookie 劫持攻击
<br>
  一个Cookie的使用过程如下:
  Step1：浏览器向服务器发起请求，这时候没有Cookie。
  Step2：服务器返回时发送Set-Cookie头，向客户端浏览器写入Cookie。
  Step3：在该Cookie到期前，浏览器访问该域下的所有页面，都将发送该Cookie。
  HttpOnly 是在 Set-Cookie 时标记的,需要注意的是，服务器可能会设置多个Cookie(多个key-value对), 而 HttpOnly 可以有选择性地加在任何一个Cookie值上.在某些时候，应用可能需要 JavaScript 访问某几项 Cookie ,这种 Cookie 可以不设置 HttpOnly 标记;而仅把 HttpOnly 标记给用于认证的关键 Cookie.
<br>
* 输入检查
<br>
  常见的Web漏洞如 XSS、SQL Injection 等, 都要求攻击者构造一些特殊字符,这些特殊字符可能是正常用户不会用到的, 所以输入检查就有存在的必要了.输入检查的逻辑,必须放在服务器端代码中实现. 如果只是在客户端使用JavaScript 进行输入检查, 很容易被攻击者绕过. 目前Web开发的普遍做法,是同时在客户端 JavaScript 中和服务器端代码中实现相同的输入检查. 客户端 JavaScript 的输入检查, 可以阻挡大部分误操作的正常用户, 从而节约服务器资源.
  输入检查一般是检查用户输入的数据中是否包含一些特殊字符, 如<,>,’,” 等. 如果发现存在特殊字符, 则将这些字符过滤或者编码.
  比较智能的“输入检查”, 可能还会匹配XSS的特征. 比如查找用户数据中是否包含了 "\<script\>", "javascript" 等敏感字符.
  这种输入检查的方式, 称为 "XSS Filter".
<br>
* 输出检查
<br>
安全的编码函数
Html 的编码使用 HtmlEncode, HtmlEncode 是一种函数实现. 它的作用是将字符转换成 HTMLEntities, 对应的标准是 ISO-8859-1.
相应地，JavaScript的编码方式可以使用JavascriptEncode.
其他的包括 XMLEn-code (其实现与HtmlEncode类似), JSONEn-code, URLEncode
<br>
<br>
<br>


## 跨站点请求伪造(CSRF Cross Site RequestForgery)
攻击者伪造的请求之所以能够被网站服务器验证通过, 是因为用户的浏览器成功发送了相关 Cookie 的缘故.
浏览器所持有的 Cookie 分为两种: 一种是 "Third-party Cookie" ,也称
"本地Cookie"; 另一种是 "Session Cookie", 又称 "临时Cookie".
两者的区别在于, Third-party Cookie 是服务器在 Set-Cookie 时指定了 Expire 时间, 只有到了 Expire 时间后 Cookie 才会失效, 所以这种 Cookie 会保存在本地；而 Session Cookie 则没有指定 Expire 时间, 所以浏览器关闭后, Session Cookie 就失效了.
在浏览网站的过程中, 若是一个网站设置了 Session Cookie, 那么在浏览器进程的生命周期内, 即使浏览器新打开了 Tab 页, Session Cookie也都是有效的. Session Cookie保存在浏览器进程的内存空间中; 而Third-party Cookie则保存在本地.
如果浏览器从一个域的页面中, 要加载另一个域的资源, 由于安全原因, 某些浏览器, 如 IE 会阻止Third-party Cookie的发送, 而另外一些浏览器, 比如说 firefox 则不会.