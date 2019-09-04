---
layout: post
title: webnote-server 实现笔记
date: 2019-06-21 15:30:35.000000000 +08:00
tags: 项目记录
---

## 创建项目

从 https://start.spring.io/ 创建项目，spring boot 版本 2.1.6，引入如下依赖：

- actuator：监控用的，还不是特别清楚怎么玩这个
- jpa
- web
- mysql
- lombok：提供了很多方便的注解，还不是特别清楚有哪些
- commons-lang3：不清楚干啥的，从 spring 课程的代码抄来的
- java-jwt
- jackson-datatype-hibernate5：不清楚干啥的，从 spring 课程的代码抄来的
- fastjson

## docker容器

- 下载 mysql：docker pull mysql 
  查看 mysql：docker images
  删除容器：docker image rm mysql
- 启动容器：
    mongo: docker run --name mongo -p 27017:27017 -v ~/docker-data/mongo:/data/db -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=admin -d mongo
    mysql: docker run --name webnote -e MYSQL_ROOT_PASSWORD=123456 -d mysql
  查看容器：docker ps / docker ps -l
  启动容器：docker start 2cb2bf53070a // 2cb2bf53070a：containerid
  进入容器：docker container exec -it 2cb2bf53070a /bin/bash
  查看容器ip: docker inspect 2cb2bf53070a | grep '"IPAddress"' | head -n 1 // 2cb2bf53070a：containerid
- mysql 操作：
    进入mysql：mysql -u root -p，接着需要输入密码
    mysql 创建用户：
      mysql -u root -p 
      CREATE USER "webnote"@"localhost" IDENTIFIED BY "123456"; // 本地登录 
      CREATE USER "webnote"@"%" IDENTIFIED BY "123456"; // 远程登录 
      quit 
      mysql -u webnote -p #测试是否创建成功
    mysql 用户授权：
      grant all privileges on webnote.* to "webnote"@"localhost"; 
    数据库操作：
      show databases;
      create database webnote; 
      use webnote;
      show tables;
      desc user; // 显示表结构
      select user,host from user;

## 开发

代码目录结构：
|-controller
|-model
|-repository
|-service

问题1：数据库连接不上：HikariPool-1 - Exception during pool initialization
解决：

