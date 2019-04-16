# HTTP和HTTPS的区别

### 概念

![ssl](https://raw.githubusercontent.com/duiying/img/master/ssl.png)  
```
SSL:
Secure Sockets Layer, 安全套接层, SSL协议位于传输层和应用层之间, 利用加密技术, 保证数据安全.

TLS:
Transport Layer Security, 传输层安全协议, 建立在SSL3.0协议规范之上, 是SSL3.0的后续版本, 有时会统称该协议为SSL协议.

HTTPS:
Hypertext Transfer Protocol Secure, 超文本传输安全协议, 是以安全为目标的HTTP通道.
HTTPS = HTTP + SSL / TLS

常见的加密算法: 对称加密、非对称加密、Hash算法

对称加密: 加密和解密使用相同的秘钥.
常见的对称加密算法: DES、3DES、DESX、Blowfish、IDEA、RC4、RC5、RC6和AES
场景: 企业内n个用户, 任意2个用户需要使用对称加密进行数据交换, 整个企业需要n*(n-1)个秘钥.
优点: 相比于非对称加密, 对称加密的加密解密速度快
缺点: 对于秘钥的管理和分发负担比较重.

非对称加密: 加密和解密使用不同的秘钥, 分别是公钥和私钥, 公钥可以公开, 私钥不可以公开.
常见的非对称加密算法: RSA、ECC(移动设备用)、Diffie-Hellman、El Gamal、DSA(数字签名用)
场景: 企业分发给用户的一般是公钥, 企业保存私钥, 因此秘钥的分发很简单.
优点: 相比于对称加密, 非对称加密安全性更高(对称加密需要发送秘钥, 非对称加密只需要发送公钥, 私钥不需要发送)
缺点: 加密解密速度慢

Hash算法: 单向算法.
通过Hash算法对对目标信息生成一段特定长度的唯一的Hash值, 却不能通过这个Hash值重新获得目标信息.
常用于不可还原的密码存储、信息完整性校验等.
常见的Hash算法: MD2、MD4、MD5、HAVAL、SHA、SHA-1、HMAC、HMAC-MD5、HMAC-SHA1

加密算法的选择:
使用非对称加密的公钥将对称加密的密钥进行加密, 然后发送出去.
接收方使用私钥进行解密, 得到对称加密的秘钥, 然后双方用对称加密算法进行数据交换.
这样集成了对称加密的速度快和非对称加密的安全性高的优点.
```

### HTTPS的工作原理
访问https://toby.com, HTTPS工作原理如下图:  
![https](https://raw.githubusercontent.com/duiying/img/master/https.png)  
![https过程](https://raw.githubusercontent.com/duiying/img/master/https过程.png)  

过程分析:  
```
1. 浏览器(客户端)将自己支持的一套加密规则(SSL版本号、加密算法版本、哈希算法版本)发送给服务器,
   服务器收到浏览器发送来的规则, 选择一组对应的算法版本.

2. 服务器将网站地址、公钥、证书颁发机构等信息以SSL证书的形式发送给客户端.

3. 客户端收到服务器发送来的证书, 检验证书的合法性, 检验ok之后客户端信任该站点.

4. 客户端生成一个随机数random, 并使用SSL证书中的公钥进行加密(非对称加密), 
   伪代码: RSA(SSL证书中的公钥,random)->密文A ,
   客户端生成一个握手信息, 如"toby", 并使用确定的HASH算法生成一个hash值,
   伪代码: HASH("toby")->hash码 ,
   使用对称加密将random和toby握手信息进行加密, 
   伪代码: encode(random,"toby")->密文B
   把密文A, hash码, 密文B发送给服务器
   
5. 服务器收到密文A, hash码, 密文B, 使用私钥对密文A进行解密,
   伪代码: RSA(SSL证书中的私钥,密文A)->random ,
   使用对称加密对密文B进行解密, 
   伪代码: decode(random,密文B)->握手信息 ,
   使用HASH算法计算出握手信息的hash值,
   伪代码: HASH(握手信息)->hash码 , 
   对比该hash码和客户端传过来的hash码是否一致, 如果一致, 服务端生成一个握手信息"hello", 再对握手信息进行对称加密,
   伪代码: encode(random,"hello")->密文 ,
   对握手信息进行hash计算, 
   伪代码: hash("hello")->hash码
   把hash码和密文发送给客户端
   
6. 客户端收到hash码和密文, 
   decode(random,密文)->握手信息, HASH(握手信息)->hash码,
   如果该hash码和服务端发送来的hash码一致, 表示客户端和服务端都存放了一个random, 且该random是相同的.
   
7. 接下来的传输客户端全部使用encode(random,明文)进行加密传输, 服务器端则使用decode(random,密文)->明文, 该加密解密是对称加密算法.
   
   
```




### 参考
- [http://jartto.wang/2017/12/03/exploration-the-encryption/](http://jartto.wang/2017/12/03/exploration-the-encryption/)
- [https://blog.csdn.net/u013320868/article/details/54090295](https://blog.csdn.net/u013320868/article/details/54090295)
- [https://www.jianshu.com/p/2cb959529c96?from=jiantop.com](https://www.jianshu.com/p/2cb959529c96?from=jiantop.com)