# Cookie

Cookie，有时也用其复数形式 Cookies，指某些网站为了辨别用户身份、进行 session 跟踪而储存在用户本地终端上的数据（通常经过加密）。（可以叫做浏览器缓存）

## Cookie特性

- 前端（浏览器）数据存储
- 后端通过http响应头进行设置
- 前端请求时，会自动添加cookie，通过http请求头传给后端
- 前端可读写
- 遵守同源策略

## Cookie属性

- domain：域，表示当前cookie属于哪个域或子域下面，可以在哪些域名下使用
- Path：表示cookie的所属路径，标识指定了主机下的哪些路径可以接受Cookie，具体可以作用到网站的哪一级，即url层级
- Expire time/Max-age：表示了cookie的有效期。expire的值，是一个时间，过了这个时间，该cookie就失效了。或者是用max-age指定当前cookie是在多长时间之后而失效。如果服务器返回的一个cookie，没有指定其expire time，那么表明此cookie有效期只是当前的session，即是session cookie，当前session会话结束后，就过期了。对应的，当关闭（浏览器中）该页面的时候，此cookie就应该被浏览器所删除了。需要注意的是，有些浏览器提供了会话恢复功能，这种情况下即使关闭了浏览器，会话期Cookie也会被保留下来，就好像浏览器从来没有关闭一样
- secure：表示该cookie只能用https传输。一般用于包含认证信息的cookie，要求传输此cookie的时候，必须用https传输
- httponly：表示此cookie必须用于http或https传输。这意味着，浏览器脚本，比如javascript中，是不允许访问操作此cookie的
- SameSite：允许服务器要求某个cookie在跨站请求时不会被发送，从而可以阻止跨站请求伪造攻击（CSRF）

## Cookie作用

- 存储个性化设置
- 存储未登录时用户唯一标识
- 存储已登录用户凭证
- 存储其他业务数据（缓存）

## Cookie的缺陷

- cookie会被附加在每个HTTP请求中，所以无形中增加了流量。
- 由于在HTTP请求中的cookie是明文传递的，所以安全性成问题。（除非用HTTPS)
- cookie的大小限制在4KB左右。对于复杂的存储需求来说是不够用的

## Cookie 登录用户凭证

- 前端提交用户名和密码
- 后端验证用户名和密码
- 后端生成用户凭证，设置cookie，通过http响应头传输给前端
- 后续，继续访问时，http请求头会携带cookie，后端通过用户凭证cookie验证用户信息，处理请求

## Cookie 生成用户凭证

- 用户ID：很容易被篡改
- 用户ID+签名：后台根据用户ID生成签名，将用户ID+签名传给前端，后台处理请求时通过验证用户ID和签名，处理请求。同时通过判断用户ID和签名的一致性，验证用户ID是否被篡改
- Session：户端第一次正常访问服务器，服务器生成一个sessionid来标识用户并保存用户信息（服务器有一个专门的地方来保存所有用户的sessionId），在response headers中作为cookie的一个值返回，客户端收到后把cookie保存在本地，下次再发请求时会在request headers中带上这个sessionId，服务器通过查找这个sessionId就知道用户状态了，并更新sessionId的最后访问时间。sessionId也可以设置失效时间。总言之cookie是保存在客户端，session是存在服务器，session依赖于cookie

## Cookie和XSS的关系

- XSS可能偷取Cookie
- 设置http-only防止Cookie被盗

## Cookie和CSRF的关系

- CSRF利用了用户的Cookie
- 攻击站点无法读写Cookie
- 最好能阻止第三方使用Cookie（SameSite）

## Cookie的安全策略

- 签名防止篡改（用户ID+签名）
- 私有变换（加密）
- http-only（防止XSS攻击）
- secure：防止HTTP数据传输过程中的窃听，盗取Cookie
- SameSite（防止CSRF攻击）