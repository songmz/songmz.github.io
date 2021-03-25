### JWT长什么样

以下就是一个jwt，由3段字符组成，可以用base64反解出来

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

### JWT真面目

可以从<https://jwt.io>这个网站进行解析

![](https://raw.githubusercontent.com/songmz/ImageHosting/master/img/20210114000158.png)

解析出来可以看到有3段

* 第一段是header，定义了加密方式和类型，格式固定，一般情况下加密方式都是`HS256`，可以通过base64反解出来。
* 第二段是载荷，也就是正文，只要是json就可以（所以叫json web token），可以通过base64反解出来。
* 第三段是签名，用来验证整个jwt是否被篡改过，这一段是签名后的值，不可以通过base64反解出来。
* 上图中右边的第三个框需要先填密钥，然后把jwt的值粘贴在左边就可以显示验证是否通过了。
* 修改右边第三个框会在左边重新生成新的jwt。

**上图中的密钥好像是反向生成jwt用的，并且同样的密钥jwt.io网站生成的jwt与代码生成的jwt不一样**

### JWT特点

* JWT是保存在客户端的，因此注销比较麻烦，也就是说没办法销毁jwt，只能等待过期。
* JWT是base64加密的，所以可以反解出明文。
* JWT有验签，所以可以防止篡改。

### 关于JWT的加密方式

JWT通常有2种加密方式，HS256和RS256。HS256是对称加密，服务端和客户端的密钥是相同的；而RS256是非对称加密，客户端只有公钥。因此RS256更安全，但通常情况下也没必要使用RS256，使用HS256就够了。

### Token和session对比

session是保存在jvm中，通过sessionId确保唯一性，可以通过spring-session来解决分布式问题

token是保存在redis中（low一点的也可以保存在DB中），原生支持分布式

### 传统Token的生成方式

1. 验证登录名密码
2. 生成一个Token，也就是uuid，或类似的唯一值
3. 将Token保存到redis或DB，key是uuid，value是userId。可以设置Token的有效期（Redis有此功能，所以不推荐DB）
4. 将Token返回给客户端

### 验证Token

1. 在请求头中传递Token
2. 从Redis中验证Token
3. 从Redis中获取Token对应的userid
4. 根据userid查询用户信息，返回给客户端

### Token缺点

1. 依赖服务器，需要占用服务器资源
2. 每次都要去服务端校验，效率低

### Token优点

1. 可以隐然数据内容，安全性高
2. 适用分布式

### JWT的优点

1. 仅在客户端保存，不占用服务器资源。
2. jwt中可以带有业务数据，客户端传递给服务端后，服务端验证jwt后，可直接使用这些业务数据，效率高。

### JWT的缺点

1. jwt没办法从服务端销毁，只能等待jwt过期。
2. jwt中的数据可以被直接看到，使用时需要注意加密。

