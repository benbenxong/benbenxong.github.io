title: 个人博客搭建
tags: []
categories: []
date: 2021-08-16 14:26:00
---
##1. 安装软件
#1.1 安装node
```
node.org
root用户
# sudo su
# node -v    
```
#1.2 安装hexo
```
# npm install -g hexo/hexo-cli
# hexo -v
# mkdir blog
# cd blog
# hexo init
...
  info Start blogging with Hexo.
# ls
启动 hexo
# hexo s (server start)
...
localhost:4000
^c 终止预览
# hexo n(new) "我的第一篇博文"
...
  我的第一篇博文.md
# vim 我的第一篇博文.md
# hexo g (generator)
# hexo s (server start)


```
##2. github 设置
2.1 github 建新项目
```
new repository
名称:<用户名>.github.io
描述: 我的HEXO博客
create

```
2.2 与github 建立ssh连接
```
# git --global user.mail "..."
# git --global user.name "..."
# ssh-agent ...
/root/.ssh/id_..pub 粘贴到github/setting/ssh

验证ssh连通性
ssh -T git@github.com
注: 如有问题加-o IPQoS=throughput
   若通过则修改root/.ssh/config
   Host *
   	IPQos=throughput
    
```

2.3 客户端安装git插件
```
# npm install hexo-deployer-git --save
设置 _config.yml
  type: git
  repo: ...git
  branch: master
  

```
2.4 github 端设置新分支 hexo
 将hexo 分支设为默认分支

2.5 新客户端电脑配置
```
# npm install hexo hexo-deployer-git --save
# git clone http://...git
# hexo n "abc"
# vim abc.md
# hexo cl
# hexo g
# hexo s
# hexo d
----
# git branch //hexo
# git add .
# git commit -m "..."
# git push

```
