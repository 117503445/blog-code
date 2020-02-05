---
title: Hello Hexo
---

## Hello World

似乎成功的建立了 Blog !

基于 Hexo + Github Pages + Github Actions

参考了 <https://hdj.me/github-actions-hexo-cicd/> 的教程

## 踩坑

本地 Hexo init 生成的脚手架其实不能跑,会输出异常

```sh
ERROR Deployer not found: git
```

只要输入

```sh
npm install --save hexo-deployer-git
```

还有就是在 *.github.io 项目 设置了自定义域名以后，发现再次更新后域名设置就消失了。

根据 <https://www.zhihu.com/question/28814437>

原来在网页上进行设置以后，其实是在根目录新建了 CNAME 文件，内容为自定义的域名。而 hexo d 的文件不包括 CNAME，所以被覆盖了。

解决方案：在 source 文件夹下也放一个 CNAME 文件

## 主题

最后搭载了 [hexo-theme-indigo](https://github.com/yscoder/hexo-theme-indigo) 主题
