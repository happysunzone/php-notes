# 会话技术(Cookie和Session)

## 目录
- [会话技术](#会话技术)
- [session的原理](#session的原理)
- [cookie和session的区别](#cookie和session的区别)
- [禁用cookie后如何使用session](#禁用cookie后如何使用session)

### 会话技术
```
# 前言
因为HTTP协议是无状态的, 一个用户在访问不用页面时, 要识别出这是同一个用户, 就要用到会话技术, 会话技术包括cookie和session

# cookie和session保存的目录
cookie保存在客户端
session保存在服务端, 保存的目录由php.ini里面session.save_path参数决定
```

### session的原理
```
前提是服务端开启了session
1. 第一次访问页面时, 服务端生成一个不重复的sessionid(当前会话id)以及命名为sess_xxx的session文件
该session文件保存在php.ini文件中指定的目录, xxx是sessionid, sessionid可以通过session_id()函数来获取

2. 服务端向客户端返回响应, 其中有响应头Set-Cookie:PHPSESSID=xxx

3. 客户端收到Set-Cookie响应头, 将sessionid写入cookie, cookie的key为PHPSESSID, value为sessionid
比如PHPSESSID=jlis2mcmv6d5hejkemom77ibm3

4. 当第二次访问页面时, 客户端会把cookie放在请求头(Request Header)中, 服务端识别PHPSESSID这个cookie
然后根据这个cookie获取当前会话ID(sessionid), 从而找到对应的session文件, 再从session文件中读取信息
```

### cookie和session的区别
```
1. cookie保存在客户端, session依赖于cookie保存在服务端, 如果禁用cookie则session也不可用
2. cookie大小有限制, session大小限制取决于服务器
3. cookie信息保存在客户端, 不安全; session保存在服务端, 安全
4. session保存在服务端会消耗服务端资源, 如果考虑到性能可以保存到cookie
```

### 禁用cookie后如何使用session
```
禁用cookie后, 服务端每次都会生成sessionid, 这样就是会话失效
解决方法:
1. 判断是否有PHPSESSID参数是否存在, 如果存在就使用这个参数来获得sessionid, 如果没有就创建一个新的sessionid
2. 在每个URL后面加上参数 PHPSESSID=sessionid
```
```php
<?php
session_start();

if(isset($_GET['PHPSESSID'])){
    session_id($_GET['PHPSESSID']);
}

$sid = session_id();
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>页面</title>
</head>
<body>
<a href="xxx.php?PHPSESSID=<?php echo $sid;?>">链接</a>
</body>
</html>
```