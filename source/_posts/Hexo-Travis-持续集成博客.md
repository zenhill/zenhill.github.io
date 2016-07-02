---
title: Hexo_Travis_持续集成博客
date: 2016-07-02 19:09:47
category: Hexo
tags:
- Hexo
- Travis
---
折腾了一个星期才把Hexo框架置放于Github的托管下，难处在于每次推送commit新文章上github,
travis持续集成会自动生成deploy与github下（有300MB空间），整理了以下的笔记，方便日后供作参考。
<!-- more -->
---

## 安装Git客户端
1. 下载Git客户端[Git](https://git-scm.com/)

---
## 注册[Github](https://github.com/)
2.创建仓库[Create Repositories](https://github.com/new)
3.仓库名称必须是(用户名.github.io)(zenhill.github.io)

---
## 配置SSH
4.设置user name和email：
```Git
$ git config --global user.name "`GitHub用户名`"
$ git config --global user.email "`GitHub注册邮箱`"
```
---
 5.生成ssh密钥:输入下面命令
```Git
 '$ ssh-keygen -t rsa -C "`GitHub注册邮箱`"'
```
 一直enter(回车)，之后会生成两个文件`id_rsa`和`id_rsa.pub`
 default在`用户文件夹.ssh下`。

---
 6.添加公钥到github：
 Github用户头像的Setting下，选择`SSH and GPG keys`·
 [Note++](https://notepad-plus-plus.org/)打开刚才生成的`id_rsa.pub`copy里面的内容
 然后paste到`SSH and GPG keys`Key文本框中点击`Add SSH Key`。

 ---
7.测试SSH链接:
```Git
'$ ssh -T git@github.com'
```
---
## 创建`本地`仓库
```Git
8.'$ mkdir blog'
9.$ cd blog # 切换到blog目录`
```
##部署Hexo
10.安装Hexo
```Git
'$ npm install hexo-cli -g'
```
