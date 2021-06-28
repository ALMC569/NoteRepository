# Net项目学习
## 知识预备
### JWT （JSON WEB Token）
#### 介绍
Json web token (JWT), 是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准（(RFC 7519).该token被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）场景。JWT的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该token也可直接被用于认证，也可被加密。
#### Token格式
由JWT技术生成的Token由Header Payload Signiture三个部分用点连接构成
Header.Payload.Signiture
每次使用Token认证需要放置在Http请求头中
	eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
#### Token每个部分讲解
##### header
形如
	{
  	'typ': 'JWT',
  	'alg': 'HS256'
	}

声明类型，这里是jwt
声明加密的算法 通常直接使用 HMAC SHA256
然后将头部进行base64加密（该加密是可以对称解密的),构成了第一部分.
	eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
##### playload

##### signiture

