# HTTP推送服务400错误

描述
```
利用OneNet平台的HTTP推送服务, 推送实时数据到个人服务器(后端Yii2.0框架), 一直推送失败. 
查看Nginx access.log, 如下:
183.230.102.84 - - [19/Apr/2019:07:39:58 +0000] "POST /index.php?r=one-net-push/push HTTP/1.1" 400 1689 "-" "OneNET" "-"
发现一直是400错误
```
原因
```
Yii2.0框架开启了CSRF验证
```
解决
```
控制器中关闭CSRF验证
public $enableCsrfValidation = false;
```