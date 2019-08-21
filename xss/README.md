# XSS

跨站脚本攻击（XSS），是目前最普遍的Web应用安全漏洞。这类漏洞能够使得攻击者嵌入恶意脚本代码到正常用户会访问到的页面中，当正常用户访问该页面时，则可导致嵌入的恶意脚本代码的执行，从而达到恶意攻击用户的目的。

## 跨站

简单来说，如果有一个网站，希望网站中所有运行的逻辑都来自本站。如果运行了别的网站的脚本，就产生了跨站脚本攻击。

## XSS攻击原理

攻击者向有XSS漏洞的网站中输入(传入)恶意的HTML代码，当用户浏览该网站时，这段HTML代码会自动执行，从而达到攻击的目的。如，盗取用户Cookie信息、破坏页面结构、重定向到其它网站等。

```javascript
<script>由数据变成了程序
```

## XSS能做什么

- 获取页面数据
- 获取Cookie信息
- 截取前端逻辑
- 发送请求
- 偷取网站任意数据
- 偷取用户资料
- 偷取用户密码和登录态
- 欺骗用户
- 等等...

## XSS攻击类型

- 反射型：攻击代码直接由url参数注入，页面直接执行了注入的代码
- 存储型：XSS代码会被保存到网站的数据中，如数据库，在其他用户访问到这条记录时，这条攻击代码会被读取出来，显示在页面上。（危害性更大）

## XSS攻击注入点

- HTML节点内容：内容是动态生成，包含用户输入的信息
- HTML属性
- Javascript代码
- 富文本

### HTML节点内容

```html
<div>
    #{content}
</div>
//XSS攻击
<div>
    <script>
       alert(1)
    </script>
</div>
```

### HTML属性

```html
<img src="#{image}"/>
//提前关闭src属性 注入攻击代码：1" onerror="alert(1)
<img src="1" onerror="alert(1)"/>
```

### Javascript代码

```javascript
<script>
    var data = "#{data}";
    //提前关闭data变量 注入攻击代码：hello"; alert(1);"
    var data = "hello"; alert(1);""
</script>
```

### 富文本

- 富文本得保留HTML
- HTML有XSS攻击风险

## XSS攻击防御方法

- 浏览器自带防御 
  - X-XSS-Protection：通过设置响应头处理XSS攻击
    - 0： 表示关闭浏览器的XSS防护机制
    - 1：启用XSS过滤（通常浏览器是默认的）。 如果检测到跨站脚本攻击，浏览器将清除页面（删除不安全的部分）
    - 1;mode=block：启用XSS过滤。 如果检测到攻击，浏览器将不会清除页面，而是阻止页面加载 
    - 1; report=```<reporting-URI>```  (Chromium only)：启用XSS过滤。 如果检测到跨站脚本攻击，浏览器将清除页面并使用CSP [report-uri](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)指令的功能发送违规报告
  - 只能处理反射型攻击类型， url参数出现在HTML节点内容或HTML属性中
  
  ```javascript
  //默认会拦截XSS攻击
  The XSS Auditor refused to execute a script in 'http://localhost/?from=%3Cscript%3Ealert(1)%3C/script%3E' because its source code was found within the request. The auditor was enabled as the server did not send an 'X-XSS-Protection' header.
  ```

## HTML节点内容防御方法

- ```转义< &lt; 和> &gt;```。

```html
<div>
    #{content}
</div>
//XSS攻击
<div>
    <script>
       alert(1)
    </script>
</div>
```

```javascript
var escapeHtml = function (str) {
    if (!str) return ''
    str = str.replace(/</g, '&lt;');//处理 <
    str = str.replace(/>/g, '&gt;');//处理 >
    return str;
}
```

## HTML属性防御方法

- ```转义" &quto;```。
- ```转义' &#39;```。
- ```转义  &nbsp;```：属性没有引号情况下，转义空格。

```html
<img src="#{image}"/>
//提前关闭src属性 注入攻击代码：1" onerror="alert(1)
<img src="1" onerror="alert(1)"/>
//属性没有引号
<img src=1 onerror=alert(1) />
```

```javascript
var escapeHtmlProperty = function (str) {
    if (!str) return ''
    str = str.replace(/"/g, '&quto;');//处理 "
    str = str.replace(/'/g, '&#39;');//处理 '
    str = str.replace(/ /g, '&nbsp;');//处理空格
    return str;
}
```

## HTML节点内容，HTML属性防御方法

```javascript
var escapeHtmlAndProperty = function (str) {
    if (!str) return ''
    str = str.replace(/&/g, '&amp;');//处理 & 需要放在最前面 一般不需要处理
    str = str.replace(/</g, '&lt;');//处理 <
    str = str.replace(/>/g, '&gt;');//处理 >
    str = str.replace(/"/g, '&quto;');//处理 "
    str = str.replace(/'/g, '&#39;');//处理 '
    //处理空格 由于html内容中，多个连续的空格，在渲染时只会产生一个空格，将空格全部转为&nbsp;（html实体）可能显示存在问题，一般情况下，空格不进行转义。基于这种情况，必须确保属性带上引号，否则不能处理属性没有引号情况下的XSS攻击
    // str = str.replace(/ /g, '&nbsp;');
    return str;
}
```

## Javascript代码防御方法

- 转义```" \"```。将```"```转义为```\"```
- 转换成josn（推荐）
  
```javascript
<script>
    var data = "#{data}";
    //提前关闭data变量 注入攻击代码：hello"; alert(1);"
    var data = "hello"; alert(1);""
</script>
```

```javascript
//不完善的方法
var escapeForJs = function (str) {
    if (!str) return ''
    str = str.replace(/\\/g, '\\\\');//将\ 转义为\\
    str = str.replace(/"/g, '\\"');//将" 转义为\"
    return str;
}

//转换成josn
var escapeForJsJson = function (str) {
    if (!str) return ''
    return JSON.stringify(str)
}
```

## 富文本防御方法

- 使用黑名单过滤，过滤不符合要求的标签和属性。实现相对简单，只需要按照正则表达式过滤。但是html是非常庞大，繁杂的，有太多的标签和属性，容易忽略，产生漏洞
- 使用白名单，保留部分标签和属性。过滤比较彻底，只允许指定的标签，属性存在。但是实现比较麻烦，需要将html完全解析成树状结构，针对这个dom树，遍历它的元素，保留白名单中的标签和属性，其他的过滤掉。过滤完之后，再组装成html。

### 使用黑名单过滤

```html
//需要过滤javascript
<a href="javascript:alert(1)">点击</a>
```

```script
//需要过滤script
<script>
    alert(2);sssss;
</script>
```

```javascript
//通过黑名单过滤
var xssFilter = function (html) {
    if (!html) return ''
    html = html.replace(/<\s*\/?script\s*>/g, '');//将script转义为空
    html = html.replace(/javascript[^'"]*/g, '');//将javascript转义为空
    return html
}
```

### 使用白名单过滤

- 使用[cheerio](https://github.com/cheeriojs/cheerio)处理XSS攻击
  - 解析html，对标签，属性进行过滤，最终返回html，需要自己维护，完善白名单列表
- 使用[js-xss](https://github.com/leizongmin/js-xss)处理XSS攻击

```javascript
//使用白名单处理富文本
const cheerio = require('cheerio')
var xssFilter3 = function (html) {
	if (!html) return ''
	const $ = cheerio.load(html)

	//定义白名单 需要进行维护，完善该名单
	const whiteList = {
		'img': ['src'],//标签名：属性列表
    }
    //过滤标签属性
	$('*').each((index, elem) => {
		//标签不存在白名单中 直接移除
		if (!whiteList[elem.name]) {
			$(elem).remove();
			return;
		}
		// //标签在白名单中 处理属性
		for (let attr in elem.attribs){
			if(whiteList[elem.name].indexOf(attr)===-1){
				//移除属性
				$(elem).attr(attr,null)
			}
		}
    })
    //返回过滤后的标签属性
	return $.html()
}
```

## CSP

HTTP 响应头Content-Security-Policy（内容安全策略）允许站点管理者控制用户代理能够为指定的页面加载哪些资源。除了少数例外情况，设置的政策主要涉及指定服务器的源和脚本结束点。这将帮助防止跨站脚本攻击（Cross-Site Script）（XSS）。如需更多信息，请查阅[Content Security Policy (CSP)](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Security-Policy)

```script
Content-Security-Policy: policy

Content-Security-Policy: default-src 'self' http://example.com;
                       connect-src 'none';
```