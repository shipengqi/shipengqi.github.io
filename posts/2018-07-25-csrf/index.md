# 关于 CSRF


最近在工作中，碰到了一个 security 的问题，产品容易被 CSRF 攻击，于是就对 CSRF 的攻击和防御原理，做了调研。

<!--more-->

## 什么是 CSRF

CSRF（Cross Site Request Forgery, 跨站域请求伪造）是一种网络的攻击方式，它在 2007 年曾被列为互联网 20 大安全隐患之一。
简单地说，攻击者盗用了你的身份，在你非自愿的情况下，以你的名义发送恶意的请求。（发送邮件，发消息，盗取你的账号，购买商品，虚拟货币转账）
CSRF 攻击之所以能够成功，是因为黑客通过借助用户的 cookie 骗取服务器的信任，伪造用户的请求。要抵御 CSRF，关键在于在请求中放入黑客所不能伪造的
信息，并且该信息不存在于 cookie 之中。

## 防御策略

在业界常用的防御 CSRF 攻击的策略有三种：

- 验证 HTTP Referer 字段
- CSRF Token
- 验证码

## 验证 HTTP Referer

### Referer

在 HTTP 请求的 Headers 部分 Referer 通常是像这样：

```
Referer http://www.baidu.com/s?tn=98835442_hao_pg&ie=utf-8&f=3&wd=126.com&oq=126.&bs=126.com&rsv_bp=1&inputT=5799&rsp=0
```

Referer 主要用来让服务器判断来源页面，通常被网站用来统计用户来源，比如是从搜索页面，还是从其他网站链接过来，或是从书签访问，方便网站定位。
Referer 有时也被用作防盗链，即下载时判断来源地址是不是在网站域名之内，否则就不能下载或显示。还可用做电子商务网站的安全，在提交信用卡等重要
信息的页面用 Referer 来判断上一页是不是自己的网站，如果不是，可能是黑客用自己写的一个表单，来提交，为了能跳过你上一页里的 javascript 的验证
等目的。

### 优点

简单易行，不需要做过多的修改，在敏感的请求前统一加拦截器检查 Referer 就可以了。

### 缺点

- Referer 的值是由浏览器给的，虽然使用最新的浏览器，黑客无法篡改 Referer 值，但是 HTML5 添加了一大堆有用的新值到 rel 属性上，其中有一个
值是 `noreferrer`。当这个属性被添加上之后，用户请求该链接资源时，浏览器不设置 Referer 头。
- 安全性依赖于浏览器来保障，并不安全，Referer 值在一些浏览器中可能被篡改，比如 IE6 或 FF2。

## CSRF Token

要抵御 CSRF，关键在于在请求中放入黑客所不能伪造的信息，并且该信息不存在于 cookie 之中。可以在用户登陆后，服务器返回一个 Token，储存在客户
端（可以将 token 存在 localstorage/sessionstorage 中）。

在请求的时候，把 Token 当做参数放到 URL 中，或者放到 Headers 的自定义属性中。服务端（可以将 token 保存在 memcache/redis）在收到请求后，
对 Token 进行校验。

> 生成 token 有很多种方法，任何的随机算法都可以使用，UUID 也是一个不错的选择。

### 优点

比验证 HTTP Referer 的方式要更安全。

### 缺点

- 把 Token 当做参数放到 URL 中，就难以保证 token 本身的安全。特别是在一些论坛之类支持用户自己发表内容的网站，黑客可以在上面发布自己个人网站的地址。
由于系统也会在这个地址后面加上 token，黑客可以在自己的网站上得到这个 token，并马上就可以发动 CSRF 攻击。为了避免这一点，系统可以在添加 token 的
时候增加一个判断，如果这个链接是链到自己本站的，就在后面添加 token，如果是通向外网则不加。
- 把 Token 放到 Headers 的自定义属性中，不用担心 token 会透过 Referer 泄露到其他网站中去，但是有局限性，就是必须使用 Ajax 方法的请求才适用。

**示例**

```javascript
// 从 headers 中取得 Referer 值
let referer = req.headers["Referer"];
// 判断 Referer 是否以 www.shipengqi.top 开头
if((referer !== null) && (referer.trim().startsWith(“www.shipengqi.top”))){
   // do someTing
   // 验证 Token
   validateToken(req, res);
}else{
   // error
}
```

## 验证码

通过在表单中添加一个随机的数字或字母验证码的方式，强制用户和应用进行交互，来有效地遏制 CSRF 攻击。

## Node.js CSRF 相关的第三方库

- [csurf](https://github.com/expressjs/csurf) CSRF token middleware for express.
- [alt-xsrf](https://github.com/inca/alt-xsrf) XSRF prevention middleware for express.
- [koa-csrf](https://github.com/koajs/csrf) CSRF token middleware for Koa.
- [koa-atomic-session](https://github.com/koajs/atomic-session) Atomic sessions for Koa.
- [csrf](https://github.com/pillarjs/csrf) Logic behind CSRF token creation and verification.

