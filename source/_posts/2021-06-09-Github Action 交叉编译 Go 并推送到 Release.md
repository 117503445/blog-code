---
title: Github Action 交叉编译 Go 并推送到 Release
date: 2021-06-09 10:18
---

## 目标

进行一次 Commit 以后，Github Action 自动将代码编译为多平台的可执行文件，并发布到 Github Release。

## Show Me Code

在 .github/workflows/release.yaml 写入

```yml
# .github/workflows/release.yaml

on:
  push: # 每次 push 的时候触发

name: Build Release
jobs:
  release:
    if: startsWith(github.ref, 'refs/tags/') # 只有这次 Commit 是 创建 Tag 时，才进行后续发布操作
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@master # checkout 代码
      - uses: actions/setup-go@v2 # 配置 Go 环境
        with:
          go-version: "1.16.4" # 改成自己的版本

      - run: go build -o oj_linux_amd64 ./cmd/oj # 这 3 条是交叉编译 Go 的指令，酌情修改。
      - run: CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build -o oj_windows_amd64.exe ./cmd/oj
      - run: CGO_ENABLED=0 GOOS=darwin GOARCH=amd64 go build -o oj_darwin_amd64 ./cmd/oj

      - name: Release
        uses: softprops/action-gh-release@v1
        with: # 将下述可执行文件 release 上去
          files: |
            oj_linux_amd64
            oj_windows_amd64.exe
            oj_darwin_amd64
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

```

为啥不是每次 Commit 都自动 Release？因为 Release 需要 tag。每次打 tag 以后自动发布，其实也很优雅了。

