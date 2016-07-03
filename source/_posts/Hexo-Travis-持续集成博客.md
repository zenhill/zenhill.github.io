---
title: Hexo_Travis_持续集成博客
date: 2016-07-02 19:09:47
category: Hexo
tags:
- Hexo
- Travis
---
把Hexo框架置放于Github的托管下，难处在于每次推送commit新文章上github,
travis持续集成会自动生成deploy于github下（有300MB空间），整理了以下的笔记，方便供作参考。

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
<!-- more -->
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
## 创建*本地*仓库
```Git
8.$ mkdir blog
9.$ cd blog # 切换到blog目录
```

## 部署Hexo(本地机器)
10.安装Hexo 本地
```Git
$ npm install hexo-cli -g
```
<blockquote>
hexo全局安装一次就够。
</blockquote>

11.初始化Hexo *本地*
```Git
$ hexo init
```
12.安装依赖 *本地*
```Git
$ npm install
```
13.添加github仓库信息hexo配置文件_config.yml：*本地*
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.com:zenhill/zenhill.github.io.git #github仓库地址
  branch: master # github主支
```
<blockquote>
注意：type、repo、branch的前面有两个空格，后面的:后面有一个空格
</blockquote>

14.安装git插件 *本地*
```
npm install hexo-deployer-git --save
```

**到这里基本上Hexo都已经布置完了，为了能够实现Travis持续集成所以到这里都没有输入hexo deploy这个cmd,因为待会让travis去实现deploy在gh-pages的分支而不是hexo deploy push 到master。**

---
## 配置Travis部署Hexo
1.在之前创建的Hexo folder里面手动生成.travis.yml，或者输入命令
```
cd hexo folder目录输入以下命令
$ touch .travis.yml
```
2.安装Travis

Travis安装需要Ruby环境并且需要安装rubygems插件
[rubygems](http://rubyinstaller.org/downloads/)

3.
```
# 安装travis （command window）
gem isntall travis
```
4.登录travis （command window）
```
travis login --auto
```
<font color=red>输入github的用户名和密码</font>

5.配置.travis.yml,以下是简单布置<font color =blue>实现</font>travis接口。添加变量到[官方网站](https://docs.travis-ci.com/)参考
```
language: node_js
branches:
  only:
  - gh-pages
before_install:
- npm install -g hexo
- npm install
install:
- hexo generate
```
<font color=green>以上设定为了让travis知道repo是出于node_js build的</font>
<font color=orange>而且只当gh-pages有所变动时实现travis build。</font>
<font color=purple>所以我们一定要在github master添加分支(branch)命名为*gh-pages*这是非常重要的，要不然travis会一直循环不停的创建</font>

6.配置Github token好让travis<font color=red>至少</font>能有access repo push的权利，创建[Personal access Token](https://github.com/settings/tokens)
<font color=red>创建了之后记得把token码copy下来，因为这码只在你创建之后显示一次。</font>

---
## 重写Hexo Config 配合使用以上创建的token。

1.接下来继续<font color=red>步骤4</font>的登录travis(command window)
```
travis login --auto
```
2.为了安全原因,使用travis加密法加密Github token,当一切顺利完成，会在.travis.yml的文件里添加信息。
```
$ travis encrypt 'GH_TOKEN=<之前生成的Github token>' --add
```
以下是.travis.yml生成的信息。
```
env:
  global:
      secure: X7+8XA.....#注意这里所生成的加密tokens每个人都不一样。
```

3.最后是最容易也是最难的一部分。现在我们要让travis动态的(每一次)commit之后
重写Hexo config 来使用网页token而不是ssh 的。以下为<font color=red>.travis.yml</font>里的代码。
```
before_script:
- git config --global user.name '用户名'
- git config --global user.email '用户邮件'
- sed -i'' "s~git@github.com:用户名/仓库名.github.io.git~https://${GH_TOKEN}:x-oauth-basic@github.com/用户名/仓库名.github.io.git~" _config.yml
```
- 完整版[.travis.yml](https://github.com/zenhill/zenhill.github.io/blob/gh-pages/.travis.yml)参考
- 完整版[_config.yml](https://github.com/zenhill/zenhill.github.io/blob/gh-pages/_config.yml)
- 完整版[package.json](https://github.com/zenhill/zenhill.github.io/blob/gh-pages/package.json)
- [参考文献1-SeayXu](http://www.jianshu.com/p/f4cc5866946b)
- [参考文献2-Graham Cox](http://sazzer.github.io/blog/2015/05/04/Deploying-Hexo-to-Github-Pages-with-Travis/)
