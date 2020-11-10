<p align="center">
  <a href="zerolab-fe.github.io/esnext-book/">
    <img width="200" src="https://raw.githubusercontent.com/zerolab-fe/esnext-book/main/assets/logo.svg">
  </a>
</p>

<h1 align="center">ECMAScript Next 系列教程</h1>

<div align="center">
  <a href="https://github.com/zerolab-fe/esnext-book/actions?query=workflow%3Adeploy">
    <img alt="GitHub actions deploy" src="https://github.com/zerolab-fe/esnext-book/workflows/deploy/badge.svg" />
  </a>
  <a href="https://github.com/zerolab-fe/esnext-book/actions?query=workflow%lint">
    <img alt="GitHub actions lint" src="https://github.com/zerolab-fe/esnext-book/workflows/lint/badge.svg" />
  </a>
  <a href="https://github.com/zerolab-fe/esnext-book">
    <img alt="GitHub license" src="https://img.shields.io/github/license/zerolab-fe/esnext-book">
  </a>
</div>

## 安装

安装依赖

```bash
$ npm install
```

全局安装 gitbook

```bash
$ npm install gitbook -g
```

## 使用

```bash
$ gitbook serve
```

## 分支规范

主干分支：`main`  
测试分支：`release`  
功能分支：`feature/*`

`feature/*`：基于 `main` 分支 checkout 新的分支，分支开发完成后发起 MR 合并到 `release` 分支  
`release`：合并到此分支的代码需要确保无误，经过严格校验。此分支在合适的时机，由管理员合并到 `main`  
`main`：合并到此分支的代码，会触发 CI 自动发布
