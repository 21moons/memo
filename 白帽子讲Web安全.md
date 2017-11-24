## 2. 浏览器安全机制:同源策略

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


## 3. XSS 攻击(Cross-site Scripting)

* 反射型XSS
* 存储型XSS
* DOM Based XSS

XSS的本质是一种 "HTML注入", 用户的数据被当成了HTML代码一部分来执行, 从而混淆了原本的语义, 产生了新的语义.
对于XSS攻击来说, JavaScript工作在渲染后的浏览器环境中, 无法控制用户浏览器发出的HTTP头.因为浏览器同源策略的原因, XSS也受到同源策略的限制——发生在A域上的XSS很难影响到B域的用户.

#### XSS 的防御
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


## 4. 跨站点请求伪造(CSRF Cross Site RequestForgery)
攻击者伪造的请求之所以能够被网站服务器验证通过, 是因为用户的浏览器成功发送了相关 Cookie 的缘故.
浏览器所持有的 Cookie 分为两种: 一种是 "Third-party Cookie" ,也称
"本地Cookie"; 另一种是 "Session Cookie", 又称 "临时Cookie".
两者的区别在于, Third-party Cookie 是服务器在 Set-Cookie 时指定了 Expire 时间, 只有到了 Expire 时间后 Cookie 才会失效, 所以这种 Cookie 会保存在本地；而 Session Cookie 则没有指定 Expire 时间, 所以浏览器关闭后, Session Cookie 就失效了.
在浏览网站的过程中, 若是一个网站设置了 Session Cookie, 那么在浏览器进程的生命周期内, 即使浏览器新打开了 Tab 页, Session Cookie 也都是有效的. Session Cookie 保存在浏览器进程的内存空间中; 而 Third-party Cookie 则保存在本地.
如果浏览器从一个域的页面中, 要加载另一个域的资源, 由于安全原因, 某些浏览器, 如 IE 会阻止 Third-party Cookie 的发送, 而另外一些浏览器, 比如说 firefox 则不会.

#### CSRF 的防御
* 验证码
* Referer Check
* Anti CSRF Token
  Token 需要同时放在表单和 Session 中. 在提交请求时, 服务器只需验证表单中的 Token, 与用户 Session(或Cookie) 中的 Token 是否一致, 如果一致, 则认为是合法请求; 如果不一致, 或者有一个为空, 则认为请求不合法,可能发生了 CSRF 攻击.
  在使用Token时, 要注意Token的保密性和随机性.
<br>
<br>
<br>

## 5. 点击劫持 (ClickJacking)

点击劫持是一种视觉上的欺骗手段. 攻击者使用一个透明的, 不可见的 iframe, 覆盖在一个网页上, 然后诱使用户在该网页上进行操作, 此时用户将在不知情的情况下点击透明的 iframe 页面. 通过调整 iframe 页面的位置, 可以诱使用户恰
好点击在 iframe 页面的一些功能性按钮上.

#### 图片覆盖攻击 (Cross Site Image Overlaying 简称XSIO)

#### 拖拽劫持与数据窃取

#### ClickJacking 3.0: 触屏劫持

#### 防御 ClickJacking
* frame busting
* X-Frame-Options
<br>
<br>
<br>

## 6. HTML 5 安全
#### iframe 的 sandbox
在 HTML 5 中, 专门为iframe定义了一个新的属性, 叫 sandbox. 使用 sandbox 这一个属性后, \<iframe\> 标签加载的内容将被视为一个独立的 "源"(源的概念请参考 "同源策略"), 其中的脚本将被禁止执行, 表单被禁止提交, 插件被禁止加载, 指向其他浏览对象的链接也会被禁止.
sandbox 属性可以通过参数来支持更精确的控制. 有以下几个值可以选择:
* allow-same-origin：允许同源访问;
* allow-top-navigation：允许访问顶层窗口;
* allow-forms：允许提交表单;
* allow-scripts：允许执行脚本;

#### Cross-Origin Resource Sharing
#### postMessage 跨窗口传递
浏览器中的 window 这个对象几乎不受同源策略限制, 很多脚本攻击都巧妙地利用了 window 对象的这一特点.
在HTML 5中, 为了丰富Web开发者的能力, 制定了一个新的API: postMessage.
postMessage 允许每一个 window(包括当前窗口, 弹出窗口, iframes等)对象往其他的窗口发送文本消息, 从而实现跨窗口的消息传递. 这个功能是不受同源策略限制的.
#### Web Storage
Web Storage 分为 Session Storage 和 LocalStorage.Session Storage 关闭浏览器就会失效, 而 Local Storage 则会一直存在. Web Storage 就像一个非关系型数据库, 由 Key-Value 对组成, 可以通过 JavaScript 对其进行操作.
Web Storage 也受到同源策略的约束, 每个域所拥有的信息只会保存在自己的域下.
<br>
<br>
<br>

# 服务端应用安全
## 7. 注入攻击
注入攻击是 Web 安全领域中一种最为常见的攻击方式. 在 "跨站脚本攻击" 一章中曾经提到过, XSS 本质上也是一种针对 HTML 的注入攻击. 而在 "我的安全世界观" 一章中, 提出了一个安全设计原则 "数据与代码分离" 原则, 它可以说是
专门为了解决注入攻击而生的. 注入攻击的本质, 是把用户输入的数据当做代码执行. 这里有两个关键条件, 第一个是用户能够控制输入; 第二个是原本程序要执行的代码, 拼接了用户输入的数据.

#### SQL 注入
#### 盲注 (Blind Injection)
#### Timing Attack
#### 编码问题
#### 解决注入问题
* 使用预编译语句
* 使用存储过程 (定义在数据库中的函数, 使用存储过程的效果和使用预编语句译类似, 其区别就是存储过程需要先将SQL语句定义在数据库中)
* 检查数据类型
* 使用安全函数
#### XML 注入
#### 代码注入 -- eval(), system()
#### CRLF 注入
CRLF实际上是两个字符:
CR是Carriage Re-turn(ASCII 13, \r)
LF是Line Feed(ASCII 10,\n)
CRLF常被用做不同语义之间的分隔符. 因此通过"注入CRLF字符", 就有可能改变原有的语义.
在HTTP协议中, HTTP头是通过"\r\n"来分隔的. 因此如果服务器端没有过滤"\r\n", 而又把用户输入的数据放在 HTTP 头中, 则有可能导致安全隐患. 这种在HTTP 头中的 CRLF 注入, 又可以称为"Http Response Splitting".
<br>
<br>
<br>
## 8. 文件上传漏洞







