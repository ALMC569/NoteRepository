# Net项目学习
## 知识预备
### JWT （JSON WEB Token）
#### 介绍
Json web token (JWT), 是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准（(RFC 7519).该token被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）场景。JWT的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该token也可直接被用于认证，也可被加密。
#### Token格式
由JWT技术生成的Token由Header Payload Signiture三个部分用点连接构成
Header.Payload.Signiture
每次使用Token认证需要放置在Http请求头中
	

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

#### Token每个部分讲解
##### header
形如

```json
{
'typ': 'JWT',
'alg': 'HS256'
}
```

声明类型，这里是jwt
声明加密的算法 通常直接使用 HMAC SHA256
然后将头部进行base64加密（该加密是可以对称解密的),构成了第一部分.

	eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9

##### playload
载荷就是存放有效信息的地方。这个名字像是特指飞机上承载的货品，这些有效信息包含三个部分

    标准中注册的声明
    公共的声明
    私有的声明

标准中注册的声明 (建议但不强制使用) ：

    iss: jwt签发者
    sub: jwt所面向的用户
    aud: 接收jwt的一方
    exp: jwt的过期时间，这个过期时间必须要大于签发时间
    nbf: 定义在什么时间之前，该jwt都是不可用的.
    iat: jwt的签发时间
    jti: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。

公共的声明 ：
公共的声明可以添加任何的信息，一般添加用户的相关信息或其他业务需要的必要信息.但不建议添加敏感信息，因为该部分在客户端可解密.
私有的声明 ：
私有声明是提供者和消费者所共同定义的声明，一般不建议存放敏感信息，因为base64是对称解密的，意味着该部分信息可以归类为明文信息。
定义一个payload:

```json
{
"sub": "1234567890",
"name": "John Doe",
"admin": true
}
```

然后将其进行base64加密，得到Jwt的第二部分。

	eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9

##### signiture
wt的第三部分是一个签证信息，这个签证信息由三部分组成：

    header (base64后的)
    payload (base64后的)
    secret

这个部分需要base64加密后的header和base64加密后的payload使用.连接组成的字符串，然后通过header中声明的加密方式进行加盐secret组合加密，然后就构成了jwt的第三部分。

```js
var encodedString = base64UrlEncode(header) + '.' + 	base64UrlEncode(payload);
var signature = HMACSHA256(encodedString, 'secret'); // TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

注意：secret是保存在服务器端的，jwt的签发生成也是在服务器端的，secret就是用来进行jwt的签发和jwt的验证，所以，它就是你服务端的私钥，在任何场景都不应该流露出去。一旦客户端得知这个secret, 那就意味着客户端是可以自我签发jwt了。

##### 如何应用
一般是在请求头里加入Authorization，并加上Bearer标注：

```js
fetch('api/user/1', {
headers: {
'Authorization': 'Bearer ' + token
}
})
```

(https://upload-images.jianshu.io/upload_images/1821058-2e28fe6c997a60c9.png)

#### 总结
##### 优点

    因为json的通用性，所以JWT是可以进行跨语言支持的，像JAVA,JavaScript,NodeJS,PHP等很多语言都可以使用。
    因为有了payload部分，所以JWT可以在自身存储一些其他业务逻辑所必要的非敏感信息。
    便于传输，jwt的构成非常简单，字节占用很小，所以它是非常便于传输的。
    它不需要在服务端保存会话信息, 所以它易于应用的扩展

##### 安全相关

    不应该在jwt的payload部分存放敏感信息，因为该部分是客户端可解密的部分。
    保护好secret私钥，该私钥非常重要。
    如果可以，请使用https协议

