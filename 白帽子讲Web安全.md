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

对于XSS攻击来说，JavaScript工作在渲染后的浏览器环境中，无法控制用户浏览器发出的HTTP头.因为浏览器同源策略的原因，XSS也受到同源策略的限制——发生在A域上的XSS很难影响到B域的用户.
<br>
<br>
<br>


## 跨站点请求伪造(CSRF Cross Site RequestForgery)