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
文件上传漏洞是指用户上传了一个可执行的脚本文件, 并通过此脚本文件获得了执行服务器端命令的能力.
文件上传后导致的常见安全问题一般有:
* 上传文件是Web脚本语言, 服务器的Web容器解释并执行了用户上传的脚本, 导致代码执行;
* 上传文件是Flash的策略文件crossdo-main.xml, 黑客用以控制Flash在该域下的行为(其他通过类似方式控制策略文件的情况类似);
* 上传文件是病毒,木马文件, 黑客用以诱骗用户或者管理员下载执行;
* 上传文件是钓鱼图片或为包含了脚本的图片, 在某些版本的浏览器中会被作为脚本执行, 被用于钓鱼和欺诈.

在大多数情况下, 文件上传漏洞一般都是指"上传Web脚本能够被服务器解析"的问题, 也就是通常所说的webshell的问题. 要完成这个攻击, 要满足如下几个条件:
* 首先, 上传的文件能够被Web容器解释执行. 所以文件上传后所在的目录要是Web容器所覆盖到的路径.
* 其次, 用户能够从Web上访问这个文件. 如果文件上传了, 但用户无法通过Web访问, 或者无法使得Web容器解释这个脚本, 那么也不能称之为漏洞.
* 最后, 用户上传的文件若被安全检查, 格式化, 图片压缩等功能改变了内容, 则也可能导致攻击不成功.


#### 绕过文件上传检查功能
在针对上传文件的检查中, 很多应用都是通过判断文件名后缀的方法来验证文件的安全性的. 但是在某些时候, 如果攻击者手动修改了上传过程的 POST 包, 在文件名后添加一个 %00 字节, 则可以截断某些函数对文件名的判断. 因为在许多语言的函数中, 比如在 C, PHP 等语言的常用字符串处理函数中, 0x00 被认为是终止符. 受此影响的环境有 Web 应用和一些服务器. 比如应用原本只允许上传 JPG 图片, 那么可以构造文件名(需要修改 POST 包)为 xxx.php[\0].JPG, 其中  [\0] 为十六进制的 0x00 字符, 对于服务器端来说, 此文件因为0字节截断的关系, 最终却会变成 xxx.php.
<br>
<br>
<br>
## 9. 认证与会话管理

认证的英文是 Authentication, 授权则是 Authorization. 
认证的目的是为了认出用户是谁, 而授权的目的是为了决定用户能够做什么.

#### Session  与认证
密码与证书等认证手段, 一般仅仅用于登录(Login)的过程. 当登录完成后, 用户访问网站的页面, 不可能每次浏览器请求页面时都再使用密码认证一次. 因此, 当认证成功后, 就需要替换一个对用户透明的凭证. 这个凭证, 就是 SessionID.
最常见的做法就是把 SessionID 加密后保存在 Cookie 中, 因为 Cookie 会随着 HTTP 请求头发送, 且受到浏览器同源策略的保护.
#### Session Fixation 攻击
在今天使用 Cookie 才是互联网的主流, SessionID 的方式渐渐被淘汰. 而由于网站想保存到 Cookie 中的东西变得越来越多, 因此用户登录后, 网站将一些数据保存到关键的 Cookie 中, 已经成为一种比较普遍的做法. Session Fixation 攻击的用武之地也就变得越来越小了.
#### Session 保持攻击
一般来说, Session 是有生命周期的, 当用户长时间未活动后, 或者用户点击退出后, 服务器将销毁 Session. Session 如果一直未能失效, 会导致什么问题呢? 前面的章节提到 Session 劫持攻击, 是攻击者窃取了用户的 SessionID, 从而能够登录进用户的账户.
但如果攻击者能一直持有一个有效的 Session (比如间隔性地刷新页面，以告诉服务器这个用户仍然在活动), 而服务器对于活动的 Session 也一直不销毁的话, 攻击者就能通过此有效 Session 一直使用用户的账户, 成为一个永久的"后
门".
在Web开发中, 网站访问量如果比较大, 维护 Session 可能会给网站带来巨大的负担. 因此, 有一种做法, 就是服务器端不维护 Session, 而把 Session 放在Cookie 中加密保存. 当浏览器访问网站时, 会自动带上Cookie, 服务器端只需要解密 Cookie 即可得到当前用户的 Session 了. 这样的 Session 如何使其过期呢? 很多应用都是利用 Cookie 的 Expire 标签来控制 Session 的失效时间, 这就给了攻击者可乘之机.
Cookie 的 Expire 时间是完全可以由客户端控制的. 篡改这个时间, 并使之永久有效, 就有可能获得一个永久有效的 Session, 而服务器端是完全无法察觉的.
#### 对抗 Session 攻击
常见的做法是在一定时间后, 强制销毁 Session. 这个时间可以是从用户登录的时间算起, 设定一个阈值, 比如3天后就强制Session过期.
但强制销毁 Session 可能会影响到一些正常的用户, 还可以选择的方法是当用户客户端发生变化时, 要求用户重新登录. 比如用户的 IP, UserAgent 等信息发生了变化, 就可以强制销毁当前的 Session, 并要求用户重新登录.
最后, 还需要考虑的是同一用户可以同时拥有几个有效 Session.若每个用户只允许拥有一个 Session, 则攻击者想要一直保持一个 Session 也是不太可能的. 当用户再次登录时, 攻击者所保持的 Session 将被"踢出".
#### 单点登录 SSO(Single Sign On)
<br>
<br>
<br>
## 10. 访问控制
#### 防火墙 ACL
#### Linux 的文件权限
#### 垂直权限管理
访问控制实际上是建立用户与权限之间的对应关系, 现在应用广泛的一种方法, 就是"基于角色的访问控制(Role-Based Access Control)", 简称 RBAC.
RBAC事先会在系统中定义出不同的角色, 不同的角色拥有不同的权限, 一个角色实际上就是一个权限的集合. 而系统的所有用户都会被分配到不同的角色中, 一个用户可能拥有多个角色, 角色之间有高低之分(权限高低). 在系统验证权限时, 只需要验证用户所属的角色, 然后就可以根据该角色所拥有的权限进行授权了.
在配置权限时, 应当使用 "最小权限原则", 并使用 "默认拒绝" 的策略, 只对有需要的主体单独配置 "允许" 的策略. 这在很多时候能够避免发生 "越权访问".
#### 水平权限管理
相对于垂直权限管理来说, 水平权限问题出在同一个角色上. 系统只验证了能访问数据的角色, 既没有对角色内的用户做细分, 也没有对数据的子集做细分, 因此缺乏一个用户到数据之间的对应关系. 由于水平权限管理是系统缺乏一个数据级的访问控制所造成的, 因此水平权限管理又可以称之为 "基于数据的访问控制".
#### OAuth
OAuth 是一个在不提供用户名和密码的情况下, 授权第三方应用访问 Web 资源的安全协议.
常见的应用 OAuth 的场景, 一般是某个网站想要获取一个用户在第三方网站中的某些资源或服务.
比如在人人网上, 想要导入用户 MSN 里的好友, 在没有 OAuth 时, 可能需要用户向人人网提供 MSN 用户名和密码.
这种做法使得人人网会持有用户的 MSN 账户和密码, 虽然人人网承诺持有密码后的安全, 但这其实扩大了攻击面, 用户也难以无条件地信任人人网.
而 OAuth 则解决了这个信任的问题, 它使得用户在不需要向人人网提供 MSN 用户名和密码的情况下, 可以授权 MSN 将用户的好友名单提供给人人网.
OAuth 涉及3个角色, 分别是:
* Server            (MSN)
* Resource Owner    (Server)
* Client            (Resource Owner)
<br>
<br>
<br>
## 11. 加密算法与随机数
* 分组加密算法
Initialization Vector(IV)+ key + 加密算法 + 加密模式
* ECB 模式(电码簿模式)是最简单的一种加密模式, 它的每个分组之间相对独立
* 链式加密模式(CBC) 则是完全不同的, 链式加密模式的分组前后之间会互相关联, 一个字节的变化, 会导致整个密文发生变化.
* 当需要加密的明文多于一个分组的长度时, 应该避免使用 ECB 模式, 而使用其他更加安全的加密模式
* 流密码加密算法
流密码的加密基于异或(XOR)操作, 每次都只操作一个字节. 但流密码加密算法的性能非常好, 因此也是非常受开发者欢迎的一种加密算法. 常见的流密码加密算法有 RC4, ORYX, SEAL等.
* Bit-flipping Attack (比特翻转攻击)
在密码学中, 攻击者在不知道明文的情况下, 通过改变密文, 使得明文按其需要的方式发生改变的攻击方式, 被称为 Bit-flipping Attack.
解决 Bit-flipping 攻击的方法是验证密文的完整性, 最常见的方法是增加带有 KEY 的 MAC (消息验证码, Message Authentication Code), 通过MAC验证密文是否被篡改.

通过哈希算法来实现的 MAC, 称为 HMAC. HMAC 由于其性能较好, 而被广泛使用.
* Padding Oracle Attack
* 密钥管理
在密码学里有个基本的原则: 密码系统的安全性应该依赖于密钥的复杂性, 而不应该依赖于算法的保密性.
* 伪随机数问题
伪随机数是由数学算法实现的, 它真正随机的地方在于"种子(seed)". 种子一旦确定后, 再通过同一伪随机数算法计算出来的随机数, 其值是固定的, 多次计算所得值的顺序也是固定的.
我们需要谨记: 在重要或敏感的系统中, 一定要使用足够强壮的随机数生成算法. 在 Java 中, 可以使用 java.security.SecureRandom.
* 加密算法选择
1. 不要使用 ECB 模式;
2. 不要使用流密码(比如RC4);
3. 使用 HMAC-SHA1 代替 MD5(甚至是代替 SHA1);
4. 不要使用相同的 key 做不同的事情;
5. salts 与 IV 需要随机产生;
6. 不要自己实现加密算法, 尽量使用安全专家已经实现好的库;
7. 不要依赖系统的保密性.

当你不知道该如何选择时,有以下建议;
1. 使用 CBC 模式的 AES256 用于加密;
2. 使用 HMAC-SHA512 用于完整性检查;
3. 使用带 salt 的 SHA-256 或 SHA-512 用于 Hashing.
<br>
<br>
<br>
## 12. Web 框架安全




