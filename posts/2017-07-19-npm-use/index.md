# NPM 使用入门


NPM 是 Node 的模块管理器，功能强大，使用简单。

<!--more-->

## npm 常用命令

### npm init

运行构建新项目的向导，初始化一个 `package.json` 文件，使用 `npm init --yes` 命令使用默认的配置来创建 `package.json` 文件。

### npm install

读取 `package.json` 文件来安装模块。`package.json` 文件中的 `dependencies` 和 `devDependencies`，分别对应生产环境需要的安装包和
开发环境需要的安装包。

### npm install <package_name>

在项目中安装模块。

### npm install <package_name>@<version>

安装指定版本的模块，版本号用 `@` 符号连接。

### npm install -g <package_name>

全局安装模块。

### npm install <package_name> --save

在项目中安装模块，并添加到项目配置文件 `package.json` 中，作为项目依赖。

### npm install <package_name> --save-dev

在项目中安装模块，并添加到项目配置文件 `package.json` 中，作为项目开发依赖（devDependency）。

### npm update <package_name>

更新指定的已安装模块。

### npm update -g <package_name>

更新指定的全局安装模块。

### npm uninstall <package_name>

从项目中移除已安装的模块。

### npm uninstall --save <package_name>

卸载模块的同时，也将他从 `package.json` 文件中移除。

### npm list

列出项目中已安装的所有模块

### npm list -g

列出系统中全局安装的所有模块

### npm -v

显示 npm 包管理器的当前版本

### npm adduser username

在 `npmjs.org` 创建一个账户

``` bash
npm adduser
Username: YOUR_USER_NAME
Password: YOUR_PASSWORD
Email: YOUR_EMAIL@domain.com
```

### npm whoami

显示 `npmjs.org` 上的账户详细信息

### npm publish

发布自己开发的模块到 `npmjs.org`，要发布模块必须先有账户

**更新时发布的模块要先修改模块的 version  否则会 error**。

### npm search <package_name>

搜索模块。可以用来检验某个包名是否已存在。

### npm root

查看当前包的安装路径。

### npm root -g

查看全局的包的安装路径。

### npm config

管理 npm 的配置路径。

``` bash
npm config set <key> <value> [-g|--global]
npm config get <key>
npm config delete <key>
npm config list
npm config edit
```

### npm login

登录 `npmjs.org`。

如果 `npm login` 输入正确的用户名和密码时仍出现 `Incorrect username or password` 的错误，

先通过 `npm config ls` 查看是否使用了非 `npmjs.org` 官网的 registry。如下：

```bash
npm config ls
; cli configs
user-agent = "npm/2.14.2 node/v4.0.0 darwin x64"

; userconfig /Users/xxx/.npmrc
email = "xxx@xx.com"
registry = "https://registry.npm.taobao.org/"
```

切换回 npm 官方库地址:

``` bash
npm config set registry https://registry.npmjs.org/
```

## Node.js 模块的版本号

npm 使用语义版本号来管理代码，语义版本号分为 `X.Y.Z` 三位，分别代表主版本号、次版本号和补丁版本号。当代码变更时，版本号按以下原则更新。

主要版本 Major：`X.0.0`  // 有大变动，向下不兼容
次要版本 Minor：`0.Y.0`  // 新增了功能，但是向下兼容
补丁版本 Patch：`0.0.Z`  // 只是修复bug

## 配置 npm 源

### 临时使用

``` bash
npm install express --registry https://registry.npm.taobao.org
```

### 全局使用

``` bash
npm config set registry https://registry.npm.taobao.org

# 验证是否成功
npm config get registry
npm info express
```

#### 切换会原来的源

```bash
npm config set registry https://registry.npmjs.org/
```

### 使用 cnpm

``` bash
npm install -g cnpm --registry=https://registry.npm.taobao.org

cnpm install express
```

