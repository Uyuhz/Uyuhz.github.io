---
title: 使用Jenkins部署Hexo
date: 2020-07-17 10:58:12
tags: Jenkins
categories: 编程
---
# 前言
> 本文通过Jenkins自动部署  

之前我的个人博客使用的是`Halo`，不过服务器到期我又懒得备份，东西基本都没了，这次心血来潮又换回`Hexo`，正好借这个机会玩下`Jenkins`  
  
目标：编写完成后，提交到Github仓库，通过Webhooks通知Jenkins进行构建，构建后上传到指定服务器

<!-- more -->
 
# 开始
## 准备工作
- 创建一个Github仓库
- 安装Jenkins
- 在Jenkins中安装`NodeJS`,`Send build artifacts over SSH`两个插件  

## 上传Hexo工程至Github
{% asset_img 提交目录.jpg %}
将图示目录上传至`Github`（最好使用私有仓库）  
由于我们的目标是在服务端进行构建，因此这里不需要上传本地构建后生产的`public`目录  

## 配置Webhooks
进入仓库的`Settings`页面内配置`Webhooks`
{% asset_img 配置webhooks.jpg %}  
`Payload URL`: 你的`Jenkins Webhook`地址  
例如你的`Jenkins`的访问地址为`http://jenkins.abc.com`，那么你的`Payload URL`就是`http://jenkins.abc.com/github-webhook/`

## 配置Jenkins
### 创建任务
进入`Jenkins`创建一个新的任务，选择`自由风格的项目`，名称比如是`myblog`
{% asset_img 新建任务.jpg %}    

### General
勾选`Github项目`，填入对应刚才上传的地址  
{% asset_img 配置General.jpg %}   
 
### 源码管理
还是填入地址，并且需要添加对应账号  
{% asset_img 源码管理.jpg %}  
 
### 构建触发器
我们已经配置了仓库的`Webhooks`，所以选择`GitHub hook trigger for GITScm polling`  
{% asset_img 构建触发器.jpg %}   

### 构建环境
环境我们只需要`NodeJS`，选择`Provide Node & npm bin/ folder to PATH` 
{% asset_img 构建环境.jpg %}   

### 构建
选择`执行shell`，构建很简单，只需要写入以下内容
```
npm install 
npm run build
```
构建完成后会在`/var/jenkins_home/workspace/myblog`目录中生成文件
{% asset_img 构建.jpg %} 

### 构建后操作
选择`Send build artifacts over SSH`  
- Source files 需要复制的文件目录，这里我们只需要`public`目录下的内容
- Remove prefix 复制后需要移除的目录前缀
- Remote directory 远程服务器的目录，这里我是放到了nginx的目录下
- Exec command 需要执行的命令
{% asset_img 构建后操作.jpg %}     

## 后续
配置完成后，可以点击立即构建或者通过提交方式触发构建，最后配置Nginx就可以直接访问了。

