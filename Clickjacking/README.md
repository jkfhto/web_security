# 点击劫持

- 点击劫持，clickjacking，也被称为UI-覆盖攻击。这个词首次出现在2008年，是由互联网安全专家罗伯特·汉森和耶利米·格劳斯曼首创的。
- 它是通过覆盖不可见的框架误导受害者点击。
- 这种攻击利用了HTML中```<iframe>```标签的透明属性，用外层假页面诱导用户点击，实际上是在隐藏的frame上触发了点击事件进行一些用户不知情的操作。

## 点击劫持特征

- 用户亲手操作的
- 用户不知情

## 点击劫持危害

- 盗取用户资金（转账，支付）
- 获取用户敏感信息

## 点击劫持防御

- Javascript中禁止内嵌：因为普通页面的top对象为window，而iframe的top对象不等于window对象。这样如果存在嵌套的iframe，对页面进行跳转，避免点击劫持。但是这种防御方式并不完善，如果攻击者设置ifame的属性sandbox="allow-forms" 时js脚本就会失效，但是表单提交不会失效
  
  ```javascript
  if (top.location !== window.location){
      top.location == window.location
  }
  ```

- X-FRAME-OPTIONS 防止内嵌：服务器端可设置HTTP响应头 "X-Frame-Options：DENY"来让浏览器主动禁止iframe内嵌
- 其他辅助手段（提高用户操作成本）

## X-Frame-Options 有三个值

```script
X-Frame-Options: deny
X-Frame-Options: sameorigin
X-Frame-Options: allow-from https://example.com/
```

- DENY：表示该页面不允许在 frame 中展示，即便是在相同域名的页面中嵌套也不允许。
- SAMEORIGIN：表示该页面可以在相同域名页面的 frame 中展示。
- ALLOW-FROM uri：表示该页面可以在指定来源的 frame 中展示
