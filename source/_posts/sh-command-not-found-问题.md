title: 'zsh:command not found 问题'
author: 杜先森
tags: hexo
  - 报错
  - z sh
categories:
  - he xo
date: 2020-02-04 15:29:00
---
## 关于mac配置hexo时遇到的问题


### zsh:command not found
* 新建一个文件夹
``` bash
mkdir ~/.npm-global
```
* 配置
``` bash
npm config set prefix '~/.npm-global'
```
<!--more-->
* 写入PATH
``` bash
export PATH=~/.npm-global/bin:$PATH
```
* 保存
``` bash
source ~/.profile
```
* 更改环境变量
``` bash
vi ~/.zshrc
```
* 按i进入编辑模式，在最后加上
``` bash
source ~/.bash_profile
```
* 按：wq 保存并退出=.=
![](http://cdn.mucjustin.cn/blog/2020-02-21-WechatIMG3.jpeg)