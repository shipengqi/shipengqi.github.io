# CentOs 安装 NVM


[NVM](https://github.com/creationix/nvm) 是 `Node.js` 版本管理工具，可以更方便的安装、更新、切换 `Node.js` 的版本。

<!--more-->

## 安装

### 安装脚本

使用 `curl` 安装：

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

使用 `wget` 安装：

```bash
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

这个脚本会克隆 `nvm` 仓库到 `~/.nvm`，并且在你的 `profile` 文件(`~/.bash_profile`, `~/.zshrc`, `~/.profile`,或者 `~/.bashrc`)中
添加下面的内容：

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

然后就可以使用 `nvm` 命令查看是否安装成功，如果这是提示找不到命令，说明环境变量没有生效，执行 `source ~/.bashrc` 或者 `~/.zshrc`。

## 简单使用

```bash
nvm
```

上面的命令会列出所有的`nvm`命令：

![nvm](/images/nvm/nvm.jpg)

### 常用命令

```bash
nvm --help                              # 显示所有信息
nvm --version                           # 显示当前安装的 nvm 版本
nvm install [-s] <version>              # 安装指定的版本，如果不存在 .nvmrc ,就从指定的资源下载安装
nvm install [-s] <version>  -latest-npm # 安装指定的版本，并且下载最新的 npm
nvm uninstall <version>                 # 卸载指定的 nodejs 版本
nvm use [--silent] <version>            # 切换使用已经安装的 nodejs 版本
nvm current                             # 查看当前 nodejs 版本
nvm ls                                  # 查看所有已经安装的 nodejs 版本
nvm ls <version>                        # 查看指定版本的 nodejs
nvm ls-remote                           # 显示远程所有可以安装的 nodejs 版本
nvm ls-remote --lts                     # 查看所有长期支持的 nodejs 版本
nvm alias <name> <version>              # 给指定的版本号添加别名
nvm unalias <name>                      # 删除已定义的别名
nvm install-latest-npm                  # 安装最新版本的 npm
nvm reinstall-packages <version>        # 重新安装指定版本的 nodejs
nvm cache dir                           # 显示 nvm 的 cache
nvm cache clear                         # 清空 nvm 的 cache
```

