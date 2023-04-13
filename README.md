# use-napi

> use @napi-rs/cli

> 对`napi-rs`的简要理解和实践，扩展个人对`前端`、`Node.js native addon`、`JavaScript`与`Rust`的认识

## napi-rs 工作流程
1. 使用rust编写n-api绑定代码，定义需要暴露给JavaScript的函数和数据结构
2. 使用node.js的扩展工具链将rust代码编译为Node.js插件
3. 在JavaScript中加载插件，并使用暴露的函数和数据结构

## 新建项目

```shell
napi new
# 填写项目名称
# 选择所要支持的平台
# 是否启用GitHub CI配置
````

## 结构说明

- `.cargo/config.toml`文件用于GitHub CI的交叉编译
- `.github/workflows/CI.yml`文件是GitHub Actions配置
- `build.rs`文件对于构建`node.js`的native addon是必要的，不可删除和移到其他地方

## Run

### Install

```shell
yarn install
```

### Build native addon

```shell
yarn build

# 生成 <project-name>.darwin-x64.node文件，是Node.js addon二进制文件
# 生成 index.js 自动生成的JavaScript绑定文件，帮助从addon 二进制export出所有的东西，并保证esm与commonJs的兼容
# 生成 index.d.ts，TypeScript定义文件
```

### Test

```shell
yarn test
```

## Publish

> 新建一个自己的`npm scope`

### 重命名

- 根据取的的名字判断是否需要重命名，不要以`@napi-rs`作为名称

```shell
napi rename
```

### 上传Git

1. 完善`package.json`中的必要字段
2. 发布到Github上

```shell
git init
git remote add origin git@github.com/yourname/cool.git
git add .
git commit -m "Init"
git push
```
3. 在Github上配置`NPM_TOKEN`(现在npm.org上生成自己的专属TOKEN)

4. Github CI 工作流监测到`push`动作，自动执行

### 发布

```shell
npm version patch 
# or
yarn version patch

git push --follow-tags
```

- 如果CI工作流没法发生错误，过段时间就可以看到自己的包已发布到`npm.org`

## `package.json`脚本解析

- `build`，用来构建rust生产或开发包的

- `artifacts`，这个命令，通常是在npm publish命令之前，download artifacts 这异步之后

- `prepublishOnly`，为发布NAPI-RS包做一些准备

## 一些问题（个人目前遇到的问题）
1. [ ] 如何将发布到npm仓库中的包用在vue等使用在浏览器中的框架中，比如需要磨平`process`在浏览器与node环境的不兼容
2. [ ] 发布前一些必要流程没有走，导致在node环境中使用时，报`<project-name>.darwin-x64.xx`找不到
3. [ ] 生成的包，如何在esm环境中正确使用
