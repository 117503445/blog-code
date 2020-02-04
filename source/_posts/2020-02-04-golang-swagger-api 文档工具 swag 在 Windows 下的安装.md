---
title: golang-swagger-api 文档工具 Swag 在 Windows 下的安装
---

## 背景

对于 Golang 而言，如果要生成 Swagger 文档，一般会使用 [Swag](https://github.com/swaggo/swag) 工具

## 官方安装

```sh
go get -u github.com/swaggo/swag/cmd/swag
```

但事实上，这会报出一大堆错误。

再去 [Release](https://github.com/swaggo/swag/releases) 看看，就很过分，只有 Linux 和 Mac 的，没有 Windows 的。

## 解决方案

### 下载编译

```sh
Git clone https://github.com/swaggo/swag.git
```

Clone 整个项目，自行编译，发现可以进行成功的编译。

### 直接下载编译结果

链接: <https://pan.baidu.com/s/19EYu7Yw6V2PAelUGEDCo9g> 提取码: 8avg
