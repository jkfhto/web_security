# CSRF

CSRF（Cross-site request forgery）跨站请求伪造，也被称为“One Click Attack”或者Session Riding，通常缩写为CSRF或者XSRF，是一种对网站的恶意利用。尽管听起来像跨站脚本（XSS），但它与XSS非常不同，XSS利用站点内的信任用户，而CSRF则通过伪装成受信任用户的请求来利用受信任的网站。与XSS攻击相比，CSRF攻击往往不大流行（因此对其进行防范的资源也相当稀少）和难以防范，所以被认为比XSS更具危险性

## 跨站

在其他网站，在用户不知情的情况下，对目标网站发出了请求，产生了影响。

## CSRF攻击攻击原理及过程

- 用户C打开浏览器，访问受信任网站A，输入用户名和密码请求登录网站A；
- 在用户信息通过验证后，网站A产生Cookie信息并返回给浏览器，此时用户登录网站A成功，可以正常发送请求到网站A；
- 用户在同一浏览器中，打开一个TAB页访问了攻击网站B；
- 攻击网站就能伪装成用户C向A网站发起请求（此时携带了用户的身份信息Cookie）

## CSRF攻击特征

- 攻击网站B向A网站发起请求
- 携带A网站的Cookie
- 不访问A网站前端
- HTTP请求头的refer指向攻击网站B

## CSRF攻击危害

- 利用用户登录态
- 用户不知情
- 完成业务请求
- 盗取用户资金（转账，消费）
- 冒充用户发帖背锅
- 损坏网站声誉
- 等等...

## CSRF攻击防御

- 通过SameSite Cookie，禁止第三方网站携带Cookie
- 不访问A网站前端，在A网站前端中加入验证信息，第三方攻击网站无法获取验证信息，后台通过判断验证信息处理请求
  - 验证码,（图形验证码）缺点是用户体验差
  - Token：Token 是在服务端产生的。如果前端使用用户名/密码向服务端请求认证，服务端认证成功，那么在服务端会返回 Token 给前端。前端可以在每次请求的时候带上 Token 证明自己的合法地位
- 验证HTTP请求头的refer，如果不是合法的域名直接拒绝请求

## SameSite Cookie

服务器端设置Cookie的[SameSite](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#SameSite_cookies)，浏览器端在跨站请求时Cookie不会被发送，从而可以阻止跨站请求伪造攻击（CSRF）。目前主流浏览器[支持情况](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#Browser_compatibility)

```script
Set-Cookie: key=value; SameSite=Strict
```

### SameSite属性值

- Strict：跨域发起的任何请求都不会携带cookie
- Lax： Lax相对于Strict模式来说，宽松了一些

|   请求类型    |      例子           |  非 SameSite  |  SameSite = Lax  |  SameSite = Strict  |
| -------------| ------------------- | ------------- | ---------------- |-------------------- |
|     link     | ```<a href="…">```  |       Y       |         Y        |         N           |
|   prerender  | ```<link rel="prerender" href="…">``` |       Y   |     Y      |         N   |
|   form get   | ```<form method="get" action="…">```  |       Y   |     Y      |         N   |
|  form post   | ```<form method="post" action="…">``` |       Y   |     N      |         N   |
|     iframe   | ```<iframe src="…">```|       Y       |         N       |         N           |
|     ajax     | ```$.get('…')```    |       Y       |         N        |         N           |
|    image     | ```<img src="…">``` |       Y       |         N        |         N           |
