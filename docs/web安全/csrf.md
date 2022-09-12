# CSRF

> https://xz.aliyun.com/t/7297#toc-0

## CSRF基础



- CSRF攻击的全称是跨站请求伪造(cross site request forgery)， **CSRF最关键的是利用受害者的Cookie向服务器发送伪造请求。**是基于客户端操作的请求伪造，是一种对网站的恶意利用。
- 举个例子，你访问网站A一个链接A，这时你还没有退出网站A，然后访问网站B，网站B中的一个按钮或图片什么的内嵌了链接A，那么点击这些图片或按钮时就会发一个http请求访问链接A，同时也会带上浏览器的cookie，而服务端验证了cookie的数据无误后以为是用户在网站A上正确的操作。  
- **CSRF与XSS的区别**：
    - XSS利用的是用户对指定网站的信任，CSRF利用是网站对用户浏览器的信任。

**CSRF攻击流程**

![](https://img-blog.csdnimg.cn/img_convert/31690c9f894def1d03290185d242125d.png)

1. 用户C打开浏览器，访问受信任网站A，输入用户名和密码请求登录网站A；

2. 在用户信息通过验证后，网站A产生Cookie信息并返回给浏览器，此时用户登录网站A成功，可以正常发送请求到网站A；

3. 用户未退出网站A之前，在同一浏览器中，打开一个TAB页访问网站B；

4. 网站B接收到用户请求后，返回一些攻击性代码，并发出一个请求要求访问第三方站点A；

5. 浏览器在接收到这些攻击性代码后，根据网站B的请求，**在用户不知情的情况下携带Cookie信息，向网站A发出请求。**网站A并不知道该请求其实是由B发起的，所以会根据用户C的Cookie信息以C的权限处理该请求，导致来自网站B的恶意代码被执行。


**CSRF攻击实现的条件**：

- 登录受信任站点WebA，并在本地生成Cookie。
- 在不登出WebA的情况下，**访问站点**WebB

**常见CSRF攻击类型**：

- GET：
    - 如果一个网站某个地方的功能，比如用户修改邮箱是通过GET请求进行修改的。如：`/user.php?id=1&email=123@163.com `，这个链接的意思是用户id=1将邮箱修改为123@163.com。当我们把这个链接修改为` /user.php?id=1&email=abc@163.com` ，然后通过各种手段发送给被攻击者，诱使被攻击者点击我们的链接，当用户刚好在访问这个网站，他同时又点击了这个链接，那么悲剧发生了。这个用户的邮箱被修改为` abc@163.com `了。

- POST

    - 在普通用户的眼中，点击网页->打开试看视频->购买视频是一个很正常的一个流程。可是在攻击者的眼中可以算正常，但又不正常的，当然不正常的情况下，是在开发者安全意识不足所造成的。

    - 攻击者在购买处抓到购买时候网站处理购买(扣除)用户余额的地址。比如：/coures/user/handler/25332/buy.php 。通过提交表单，buy.php处理购买的信息，这里的25532为视频ID。那么攻击者现在构造一个链接，链接中包含以下内容。

        ```html
        <form action=/coures/user/handler/25332/buy method=POST>
        <input type="text" name="xx" value="xx" />
        </form>
        <script> document.forms[0].submit(); </script> 
        ```

        当用户访问该页面后，表单会自动提交，相当于模拟用户完成了一次POST操作，自动购买了id为25332的视频，从而导致受害者余额扣除。

 		其实可以看出，CSRF攻击是源于WEB的隐式身份验证机制！WEB的身份验证机制虽然可以保证一个请求是来自于某个用户的浏览器，但却无法保证该请求是用户批准发送的！

## CSRF漏洞的挖掘：

- 最简单的方法就是抓取一个正常请求的数据包，如果没有Referer字段和token，那么极有可能存在CSRF漏洞

- 如果有Referer字段，但是去掉Referer字段后再重新提交，如果该提交还有效，那么基本上可以确定存在CSRF漏洞。

- 利用工具：
    - CSRFTester：
        - CSRF漏洞检测工具的测试原理如下：使用CSRFTester进行测试时，首先需要抓取我们在浏览器中访问过的所有链接以及所有的表单等信息，然后通过在CSRFTester中修改相应的表单等信息，重新提交，这相当于一次伪造客户端请求。如果修改后的测试请求成功被网站服务器接受，则说明存在CSRF漏洞，当然此款工具也可以被用来进行CSRF攻击。
    - **BurpSuite**自带的CSRF POC：

- JSON格式的CSRF利用：
    - 通过POST方法，且传输的数据为JSON的情况下，在目标服务器不允许跨域，且限定Content-Type 必须为 application/json。在使用Form标签的情况下，是只能设定为text/plain ，要不然json数据就会被默认进行URL编码。json请求通过js的ajax请求，但是恶意网页和源服务器不是同一个域名，受到同源策略的影响，没法跨域。
    - 没有解决办法，要利用还得看具体场景。推上搜搜 json csrf就有了。

    

## CSRF的利用



## 防御CSRF攻击

- **samesite**：

    > https://lbh.io/2016/07/10/SameSite-Cookie%E2%80%94%E2%80%94%E9%98%B2%E5%BE%A1-CSRF-XSSI/
    >
    > https://zhuanlan.zhihu.com/p/271850279

    - `SameSite-cookies` 是 Google 开发的用于防御 CSRF 和 XSSI（Cross Site Script Inclusion，跨域脚本包含）的新安全机制，只需在 `Set-Cookie` 中加入一个新的字段属性，浏览器会根据设置的安全级别进行对应的安全 cookie 发送拦截。

    - 谷歌浏览器在 version 84 默认开启该特性，火狐浏览器 69 可以再`about:config`中设置`network.cookie.sameSite.laxByDefault`来体验该特性。Edge 浏览器计划将该特性设为默认。

    - **samesite：禁止第三方网站使用本站Cookie**,**也就是第三方网站表单提交不会携带原网页的cookie了。**

    - 当用户在网站`www.web.dev`，向`static.web.dev`请求一个图片，这是一个`same-site`请求。

        不仅仅是顶级域名例如`.com`，也包括了一些公共服务`github.io`。

        有一个[公共后缀名单](https://publicsuffix.org/list/)定义了 sameSite 的范围，这使的`your-project.github.io`和`my-project.github.io`为两个站点，也就是跨域。 

    - 需要在 `Set-Cookie` 中加入 `SameSite` 关键字，例如

        `Set-Cookie: key=value; HttpOnly; SameSite=Strict`

    - 后端在设置Cookie时候给`SameSite`的值设置为`Strict`或者`Lax`。

        - 当设置`Strict`的时候代表第三方网站所有请求都不能使用本站的Cookie。
        - 当设置`Lax`的时候代表只允许第三方网站的`GET`表单、`<a>`标签和`<link>`标签携带Cookie。
            当设置`None`的时候代表和没设一样。

    - Strict 下的 Request Cookie 变化

        - 当我们通过其他网站来访问一个有 SameSite-Cookies 机制的网站时，例如从 a.com 点击链接进入 b.com，如果 b.com 设置了 `Set-Cookie:foo=bar;SameSite=Strict`，那么，`foo=bar` 这一 cookie 是不会随着 request 发送的，如下图：

- 尽量采用post类型传参，这就减少了请求被直接伪造的可能。
- **使用验证码，只要是涉及到数据交互就先进行验证码验证**，这个方法可以完全解决CSRF。但是出于用户体验考虑，网站不能给所有的操作都加上验证码。因此验证码只能作为一种辅助手段，不能作为主要解决方案。
- **验证HTTP Referer字段，该字段记录了此次HTTP请求的来源地址**，最常见的应用是图片防盗链。
    - 如何绕过
    - **这种方法只能防御来自站外的CSRF，却无法防御来自站内的CSRF；**在靶机内通过其它技术写入含有csrf代码的网页，再让受害者去点击
    -  判断Referer是否存在某关键词，必须有这个关键词才能成功提交表单。
        - 比如Referer判断存在不存在google.com这个关键词，在网站新建一个google.com目录 把CSRF存放在google.com目录,即可绕过。
    - 判断referer是否有某域名 不验证是否为根目录。
        - 判断了Referer开头是否以126.com以及126子域名 不验证根域名为126.com 那么我这里可以构造子域名x.126.com.xxx.com的服务器
    - 判断referer
- **为每个表单添加令牌token并验证**：
    - 每次访问页面都会得到一个token，刷新页面，token就会变。**后端生成一个token放在session中并发给前端。**前端发送请求时携带这个token
    - 后端通过校验这个token和session中的token是否一致判断是否是本网站的请求。
    - 恶意网页先请求一次目标网页，拿到token不就行了？
        - **因为同源策略，恶意网页不能访问到服务器返回的cookie和表单的数据，自然也就不知道token。**
        - **js也不能跨域，无法使用ajax。**
    - cookie放token，可以利用其它技术覆盖cookie里的token，对于session里面放token，因为在服务器里面，所以我们只能想办法得到表单里的token，所以可以利用其它页面存储型xss得到token，并构造csrf。
- 在 HTTP 头中自定义属性并验证 
- **在表单提交页面添加一些除本人外不知道的信息，**比如修改密码时输入源密码，攻击者由于不知道原密码，所以就不能构造csrf.





