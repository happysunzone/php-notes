# CGI、FastCGI、PHP-FPM、PHP_CGI

### CGI
```
CGI: 通用网关接口(Common Gateway Interface), 是Web Server(比如Nginx、Apache)和Web Application(比如PHP、Perl)之间数据通信的协议
CGI是一种协议, CGI可以用任何一门语言编写, 只要这门语言有标准输入、输出和环境变量, 如PHP、Perl等

CGI的工作方式: fork-and-execute
Nginx收到连接, Nginx请求操作系统fork一个CGI解释器子进程(比如PHP-CGI),
该进程根据请求提交的参数解析PHP, 然后以CGI规定的格式返回内容到Nginx, Nginx进行输出,
该进程处理完一个请求后便被销毁, 下一个请求到来时再fork新的子进程, 有多少请求就会fork多少个cgi子进程, 这就是CGI协议性能低下的原因
```

### FastCGI
```
FastCGI是用来提高CGI性能的, 它和CGI一样, 也是一种通信协议, FastCGI像是常驻(long-live)型的CGI

FastCGI的工作方式: 进程池
Nginx启动时载入FastCGI, FastCGI初始化, 创建一个主(Master)进程和多个CGI解释器进程(Worker进程), 等待Nginx的连接
Nginx收到连接, FastCGI选择一个CGI解释器进程(Worker进程), 然后以CGI规定的格式返回内容到Nginx, Nginx进行输出,
当处理完成后, Worker进程便等待并处理下一个连接请求
```

### PHP-CGI
```
PHP自带的CGI解释器
```

### PHP-FPM
```
PHP-FPM是PHP针对FastCGI协议的具体实现, PHP-FPM是一个完全独立的程序, 不依赖于PHP-CGI和PHP, 
因为PHP-FPM内置了PHP解释器, 它启动时能够自行读取php.ini配置和php-fpm.conf配置

PHP-FPM的工作方式: 进程池
Nginx启动时载入PHP-FPM, PHP-FPM初始化, 创建一个主(Master)进程和多个CGI解释器进程(Worker进程), 等待Nginx的连接
Nginx收到连接, PHP-FPM选择一个CGI解释器进程(Worker进程), 然后以CGI规定的格式返回内容到Nginx, Nginx进行输出,
当处理完成后, Worker进程便等待并处理下一个连接请求
```