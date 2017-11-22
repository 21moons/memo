## XSS 攻击(Cross-site Scripting)

* 反射型XSS
* 存储型XSS
* DOM Based XSS

对于XSS攻击来说，JavaScript工作在渲染后的浏览器环境中，无法控制用户浏览器发出的HTTP头.因为浏览器同源策略的原因，XSS也受到同源策略的限制——发生在A域上的XSS很难影响到B域的用户.



## 跨站点请求伪造(CSRF Cross Site RequestForgery)