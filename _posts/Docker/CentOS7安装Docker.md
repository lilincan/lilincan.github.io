---
layout: post
title: CentOS7安装Docker
date: 2020-04-23 01:11:36
categories:  Centos7 Docker CentOS安装Docker
---
## 环境介绍 ##

1. 服务器代理商：腾讯

2. 服务器版本： CentOS7.7.1908

## 移除已安装的旧版本 ##

```shell
sudo yum remove -y docker \\
                   docker-client \\
                   docker-client-latest \\
                   docker-common \\
                   docker-latest \\
                   docker-latest-logrotate \\
                   docker-logrotate \\
                   docker-engine
```

## 安装依赖 ##

```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
  
## 添加Docker仓库 ##

 ```shell
 sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
 ```

## 安装Docker ##

```shell
sudo yum install docker-ce docker-ce-cli containerd.io
```

## 启动Docker ##

```shell
sudo systemctl start docker  

sudo systemctl enable docker  
```
