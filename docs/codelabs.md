
**Codelabs** 主要是帮助大家能够实现一些简单/有用/好玩的东西

欢迎大家给自己的 APP 写上步骤并上传到这里！

[Codelabs 主页](https://codelabs.compose.net.cn/)


| 实验室 | 介绍 |
| -------|------|
| [**诗句卡片**](https://codelabs.compose.net.cn/codelabs/诗句卡片/index.html?index=..%2F..index#0)| 实现简单的结构布局 |


## 关于如何贡献 Codelabs 或者修改错误

[**Fork**](https://github.com/compose-museum/codelabs) Codelabs 的项目

### 你需要准备以下的环境
1. [Go](https://golang.org/dl/)
2. [Node.js](https://nodejs.org/en/download/) 和 [npm](https://www.npmjs.com/get-npm)
3. [Claat](https://github.com/googlecodelabs/tools/tree/master/claat#install)，推荐用 go 命令来安装

### 接下来

1. 在项目根目录运行 `npm install`
2. 运行 `gulp serve --codelabs-dir=codelabs`
3. 在 `http://localhost:8000/` 中查看网页


### 修改

1. 进入 codelabs 文件夹下，可以看到有许多文件夹和 `.md` 文件
2. 修改对应的 `.md` 文件
3. 执行 `claat export xxxx.md`，成功的话会看到 ok -----  并且生成一个文件夹(如果已有的话会覆盖)
4. 刷新 `http://localhost:8000/`


!!! warning "注意"
    如果是 windows 系统，请务必使用 `Unix LF` 行尾来保存文件，否则在网页上会有很多错误

    每次更新了 `.md` 需要手动输入 `claat export xxxx.md` 文件，目前 **claat** 似乎还不支持热更新 TAT

### 提交更改

pr 到 -> **gh-pages** 分支

### 提交 issue

如果觉得自己修改有点麻烦的话，也可以到[这里](https://github.com/compose-museum/codelabs/issues/new/choose)提交 `issue`，尽量提供是哪些页面/代码有错误
