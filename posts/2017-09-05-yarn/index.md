# 使用 Yarn 代替 NPM


Yarn 是 Facebook 最新的包管理器。Yarn 的使用方式跟 npm 类似，但命令上还是有所区别。

Yarn 官网：<https://yarnpkg.com/zh-Hans/>

<!--more-->

## 安装

参考 [Yarn 官方安装文档](https://yarnpkg.com/zh-Hans/docs/install#windows-stable) 。

### 源码安装

1. [下载源码](https://github.com/yarnpkg/yarn/releases) 。

```bash
wget https://github.com/yarnpkg/yarn/releases/download/v1.9.4/yarn-v1.9.4.tar.gz

mkdir /usr/local/yarn/
mv ./yarn-v1.9.4.tar.gz /usr/local/yarn/
```

2. 解压源码

```bash
tar -xvf yarn-v1.9.4.tar.gz
```

3. 配置环境变量

```bash
vim /etc/profile


# yarn
export YARN_HOME=/usr/local/node/yarn-v1.9.4
export PATH=$YARN_HOME/bin:$PATH

# 使配置生效
source /etc/profile
```

4. 验证

```bash
yarn --version
```

## 简单使用

### 开始新项目

```bash
yarn init
```

### 添加依赖包

```bash
yarn add [package]
yarn add [package]@[version]
yarn add [package]@[tag]
Adding a dependency to different categories of dependencies
```

分别添加到 `devDependencies`、`peerDependencies` 和 `optionalDependencies`：

```bash
yarn add [package] --dev
yarn add [package] --peer
yarn add [package] --optional
```

### 升级依赖包

```bash
yarn upgrade [package]
yarn upgrade [package]@[version]
yarn upgrade [package]@[tag]
```

### 移除依赖包

```bash
yarn remove [package]
```

### 安装项目的全部依赖

```bash
yarn
# 或者

yarn install
```

### 全局

```bash
yarn global [add|bin|ls|remove|upgrade] [flags]

# 相当于

npm -g
```

更多命令详情 [Yarn CLI](https://yarnpkg.com/zh-Hans/docs/cli/)

## 配置淘宝源

### 全局使用

```bash
yarn config set registry http://registry.npm.taobao.org
```

#### 切换会原来的源

```bash
yarn config set registry https://registry.npmjs.org/
```

## 避免与 npm 混用

`yarn` 的全局 `node_modules` 位置和 `npm` 的不同。`npm` 的 `bin` 目录在 `C:/Users/xxx/AppData/Roaming/npm`，`yarn` 会
把 `C:/Users/xxx/AppData/Roaming/npm/node_modules` 目录的结构改掉，并把之前用 npm 安装的一些模块删除掉，包括 npm 本身依赖的模块，
最终导致`npm`不可用。

## global add 的命令报错 command not found

```bash
yarn global bin
```

先使用上面的命令查看 `yarn` 全局安装的二进制文件的位置，找到后把这个目录配置到环境变量。

