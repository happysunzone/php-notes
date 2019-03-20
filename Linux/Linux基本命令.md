# Linux基本命令

### 文件基本操作
```
# linux文件属性 -rw-r--r--
    # 第1位表示文件类型 d目录 -文件 l链接 b存储设备 c串行设备
    # 第2-4位表示属主权限
    # 第5-7位表示属组权限
    # 第8-10位表示其它用户权限

# 返回到上一次的目录
    cd -

# 在PATH变量指定的路径中, 搜索某个系统命令的位置
    # 查看bash命令的位置
    which bash
    which sh
# bash 和 sh的区别
[root@10-9-50-240 scripts]# which bash
/usr/bin/bash
[root@10-9-50-240 scripts]# which sh
/usr/bin/sh
[root@10-9-50-240 scripts]# ll /usr/bin/sh
lrwxrwxrwx 1 root root 4 Aug 14  2018 /usr/bin/sh -> bash
    
# 创建目录 
    mkdir 目录名
# 递归创建目录
    mkdir -p 目录名
    
# 查看目录下文件
    ls
    # 查看目录下文件的详细信息
        ll
    # 以方便阅读的方式显示
        ll -h
    # 查看文件i节点
        ll -i

# 新建文件
    touch xxx
    
# 往文件中写内容
    # 文件不存在则新建文件并写入, 文件已存在则文件内容清空再写入
        echo hello > 1.txt
    # 文件不存在则新建文件并写入, 文件已存在则追加写入
        echo hello >> 1.txt

# 查看文件内容
    cat 文件名
# 实时查看文件最后n行(默认10行)内容
    tail -f 文件名
# 实时查看文件最后n行内容(5表示行数)
    tail -f -n 5 文件名

# 删除文件
    rm xxx
# 删除目录
    rm -r xxx
    
# 显示当前所在目录
    pwd

# 复制
    # 复制文件
        cp 文件名 新文件名
    # 复制目录 -r表示递归复制
        cp -r 目录名 新目录名

# 移动
    # 移动文件
        mv 文件名 目录[/新文件名] # 新文件名是可选项,表示移动并重命名
    # 移动目录
        mv 目录名 目录[/新目录名] # 新目录名是可选项,表示移动并重命名
    # -f表示覆盖已存在的文件或目录
        mv -f 文件名/目录名 目录[/新文件名或新目录名]
```

### yum(基于rpm的软件包管理器)

```
# 安装
    yum -y install xxx
# 查看软件包信息(已安装或可安装)
    yum info xxx
# 查看已安装的软件包
    yum list installed | grep xxx
# 查看已安装或可安装的软件包
    yum list | grep xxx
# 查看已安装或可安装的软件包信息
    yum list xxx
# 卸载
    yum -y remove xxx
# 查看已安装或可安装的软件包信息
    yum search xxx
# 升级所有包的同时也升级软件和系统内核
    yum -y update
# 只升级所有包,不升级软件和系统内核
    yum -y upgrade
```

### 常用软件的安装
```
# unzip
    yum -y install unzip
# zip
    yum -y install zip
# git
    yum -y install git
# which
    yum -y install which
# crontab
    yum -y install cronie
```

### 防火墙相关设置
```
# centos7默认使用firewall作为防火墙

# 启动/停止/重启
    service firewalld start/stop/restart

# 查看状态
    firewall-cmd --state

# 开放端口
    # --permanent参数表示永久生效 ,如果不加该参数重启后失效
    # --add-port=端口号/协议
    # 开放某个端口,比如80
        firewall-cmd --permanent --add-port=80/tcp
    # 开放指定范围端口,比如8080-8085
        firewall-cmd --permanent --add-port=8080-8085/tcp
        
# 删除端口
    # 如果开放的是端口范围, 删除的时候不能删除其中某个指定的端口
        firewall-cmd --permanent --remove-port=80/tcp
        firewall-cmd --permanent --remove-port=8080-8085/tcp
        
# 查看开放的端口
    firewall-cmd --permanent --list-ports
    
# 查看开放的服务
    firewall-cmd --permanent --list-services
    
# 修改完端口之后需要重新加载配置
    firewall-cmd --reload
```

### 压缩与解压缩
```
# unzip
    unzip xx.zip
    # 解压到指定目录
        unzip xx.zip -d 目录名

# zip
    # 把当前目录下的yii2目录压缩成xx.zip
        zip -r xx.zip yii2 # -r参数表示递归压缩子目录下所有文件
        
# tar解压缩
    tar -xzvf *.tar.gz
```

### 查看错误
```
# nginx
    systemctl status nginx.service -l
```

### 服务器硬件资源和磁盘操作
```
# 查看内存占用情况
    free # 详细说明见GitHub
    free -m # -m表示以M为单位显示
    free -h # -h表示以方便阅读的方式显示
    
# 查看磁盘占用情况
    df
    df -h # -h表示以方便阅读的方式显示
    
# 查看系统版本
    cat /etc/redhat-release

# 查看系统资源使用情况
    top
    
# 查看系统内核版本
    uname -r
```

# 开关机
```
# 重启
    reboot
```

# 服务
```
# 服务的启动/关闭/重启
    service docker start/stop/restart
    systemctl start/stop/restart docker.service
    
# 查看服务状态
    service docker status
    systemctl status docker.service
```