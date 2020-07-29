---
title: Docker安装Mysql
date: 2020-04-23 01:22:42
tags:
    - CentOS
    - Docker
    - Mysql
    - Docker安装Mysql
---

## 环境介绍 ##

1. 服务器代理商：腾讯云

2. 服务器版本： CentOS7.7.1908

3. Docker版本：19.03.6

## 拉取mysql5.7 ##

1. 拉取制定版本镜像

    ```shell
    sudo docker pull mysql:5.7
    ```

2. 拉取最新镜像

    ```shell
    sudo docker pull mysql:latest
    /*或者*/
    sudo docker pull mysql
    ```

## 安装MySQL ##

1. 安装并启动mysql5.7。  <a href="#" name="installMysql"></a>
    解释下下面的命令：
    a. 映射本机3308端口到mysql。
    b. -v是用来配置参数的指定对应目录的（我这里是把我的操作系统中的`/opt/docker/mysql`下的目录对应到相应目录下，可以根据自己的情况去编写）。
    c. --name用来设置安装后的名字，可以自定义（我这儿改成了mysql573308）。
    d. -e就是设置环境变量（我这儿是用来设置mysql的root用户的初始密码）。
    e. -d是说要当作守护进程启动。
    f. mysql:5.7就是指定软件包及其版本号
    g. 其他命令可以用`docker run --help`查看。
    <font color="red">注意:记录下mysql最后返回的容器ID(CONTAINER ID)，后面要用。如果忘了的话，运行`docker ps`查看CONTAINER ID就行了（我的是a7865a86dbda）。</font>  

    ```shell
    sudo docker run -p 3308:3306 --name mysql573308 \
    -v /opt/docker/mysql/conf:/etc/mysql \
    -v /opt/docker/mysql/logs:/var/log/mysql \
    -v /opt/docker/mysql/data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=123456 \
    -d mysql:5.7
    ```

2. 添加镜像开机启动，通过此命令修改  
    解释下这个命令：就是把容器ID为a7865a86dbda设置为跟随docker启动，如果你的docker是开机启动的拿它跟着启动。  

    ```shell  
    sudo docker update --restart=always a7865a86dbda
    ```

## Shell操作Mysql ##

1. 进入容器的bash:  
    就是进入容器id为a7865a86dbda所在的虚拟环境的bash，后面就是普通的shell命令了。

    ```shell
    docker exec -it a7865a86dbda /bin/bash
    ```

2. 登录容器内的mysql:  
    输入下面的命令，输入root密码（我前面设置的`MYSQL_ROOT_PASSWORD=123456`，因此密码就是123456）

    ```shell
    mysql -uroot -p
    ```

3. 修改root初始密码并删除root远程登录 设置账号密码永不过期  
    首先禁用了root用户的远程登录权限，避免被人黑了（我第一天安装完了没改密码，第二天就给我要比特币了。）

	```sql  
    /*修改root初始密码*/  
    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root@rootpassword';  

    /*删除root远程登录*/  
    DELETE FROM user WHERE host = "%" AND user = "root";  

    /*设置账号密码永不过期*/  
    ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;  

    flush privileges;  
	```

4. 创建远程账号  
    启用新的账号进行远程登录，否则root禁用了，自己也用不了数据库了。

	```sql  
    CREATE USER 'myaccount'@'%' IDENTIFIED BY 'myaccount@newpassword';  

    /* 给远程登录用户授权  */  
    GRANT ALL ON databasename.tablename TO 'myaccount'@'%';

    flush privileges;  
	```



## 设置主从同步 ##

1. 首先再部署一个mysql实例，端口号修改为3309，其他都和之前一样；
2. 在上面<a href="#installMysql">安装Mysql第1节</a>



## 参考文献 ##

 1. [博客园-MySQL5.7 添加用户、删除用户与授权](https://www.cnblogs.com/xujishou/p/6306765.html)
