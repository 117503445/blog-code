---
title: Hello Hexo
---

## Hello World

似乎成功的建立了 Blog !

基于 Hexo + Github Pages + Github Actions

参考了 <https://hdj.me/github-actions-hexo-cicd/> 的教程

## 踩坑

唯一的坑就是本地 Hexo init 生成的脚手架其实不能跑,会输出异常

```sh
ERROR Deployer not found: git
```

只要输入

```sh
npm install --save hexo-deployer-git
```

## 主题

最后搭载了 [hexo-theme-indigo](https://github.com/yscoder/hexo-theme-indigo) 主题
