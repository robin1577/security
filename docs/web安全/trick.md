# Trick

## 利用URL scheme绕过URL pathname 检查

- [**一个真实 case 及其绕过**](https://mp.weixin.qq.com/s/SysCJTcYRpV6dj9QfeQYmQ)
    - 几个月前发现某网站上有类似于下面的逻辑，会从 URL 中取 next 参数的值，并解析出 pathname 部分，执行跳转。
    
      ```javascript
      const getParam = (key) => {
      return new URL(location).searchParams.get(key)
      }
      
      const nextURL = getParam('next')
      if (nextURL) {
      	const u = new URL(nextURL)
      	location.href = "" + u.pathname
      }
      ```
    
    - 如果 u.pathname 能够以 javascript: 开头，那么就可以执行 XSS 攻击了。然而，pathname 会多带一个 ‘/’ ，导致利用失败，无法 XSS。
    
        ```shell
        new URL('http://www.xxx.com/javascript:alert(1)').pathname
        
        '/javascript:alert(1)'
        ```
    
    - 这个问题的突破点在于 new URL(M).pathname 。根据 [whatwg 的规范](https://url.spec.whatwg.org/#dom-url-pathname)，如果cannot-be-a-base-URL 为 true，那么 pathname 等价于 `path[0]`。
    
    - 只要构造`?next=urn:javascript:alert(location.origin)`即可绕过改限制并执行 XSS 攻击。
    
    - **所有ur开头的scheme，还有blob:、data:，这些“是一个url，又不全是一个url”的url，其pathname是不会有/前缀的。**
    