---
layout: "post"
title: "node版本管理工具nvm"
date: "2017-11-24 21:27"
author: "webkong"
categories:
- Node
tags:
- NVM
---

#### NVM Github地址

linux ：[https://github.com/creationix/nvm](
https://github.com/creationix/nvm)

windows: [https://github.com/coreybutler/nvm-windows](https://github.com/coreybutler/nvm-windows)

<!-- more -->
官方github有详细的安装和使用方法

#### 卸载已安装到全局的 node/npm

如果之前是官网下载安装，或者是brew安装建议，先删除node和全局模块

```
npm ls -g --depth=0 #查看已经安装在全局的模块，以便删除这些全局模块后再按照不同的 node 版本重新进行全局安装

sudo rm -rf /usr/local/lib/node_modules #删除全局 node_modules 目录
sudo rm /usr/local/bin/node #删除 node
cd  /usr/local/bin && ls -l | grep "../lib/node_modules/" | awk '{print $9}'| xargs rm #删除全局 node 模块注册的软链
```


#### 安装（linux）

通过curl或wget

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.6/install.sh | bash
```

```
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.6/install.sh | bash
```

添加`.bashrc`或`.bash_profile`或`.zshrc`

```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

查看安装成功

```
nvm --version
```

如果没有输出，执行

```
source ~/.bashrc 或 ~/.bash_profile 或 ~/.zshrc
```

#### 安装node

```
nvm install 6.11.3
```

#### 使用nvm

```
nvm ls-remote #所有可以安装的版本

nvm install 版本号 #安装某个版本的node

nvm use 版本号 #切换到某个版本的node

nvm current # 查看当前使用的版本

nvm ls #系统已安装的版本

nvm alias default node #给node加别名

nvm run 6.10.3 app.js  使用6.10.3版本运行js
```

#### 使用 .nvmrc 文件配置项目所使用的 node 版本

如果默认的版本和要使用的版本不同，但是又不想切换版本，可以使用`.nvmrc`来指定node

```
$ echo "5.9" > .nvmrc

$ echo "lts/*" > .nvmrc # to default to the latest LTS version
```

```
$ nvm use
Found '/path/to/project/.nvmrc' with version <5.9>
Now using node v5.9.1 (npm v3.7.3)
```

#### 最后

在成功安装之后， node 的真是安装位置是在

```
/Users/<username>/.nvm/versions/node
```
会有安装的所有版本， 每个版本中都有自己的npm方便管理和使用

就可以像以往一样使用node了。

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

####其他

如果 `nvm ls-remote` 返回 `N/A`，可以使用非HTTPS的源

```
export NVM_NODEJS_ORG_MIRROR=https://nodejs.org/dist

或者

export NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node
```

如果还是不行可修改nvm.sh文件，在curl加上-k以非加密形式请求。

```
nvm_download() {
  if nvm_has "curl"; then
    curl -k $*
  elif nvm_has "wget"; then
    # Emulate curl with wget
```


```
nvm alias @8 8.9.1

```

命令行内容
```
Usage:
  nvm --help                                Show this message
  nvm --version                             Print out the installed version of nvm
  nvm install [-s] <version>                Download and install a <version>, [-s] from source. Uses .nvmrc if available
    --reinstall-packages-from=<version>     When installing, reinstall packages installed in <node|iojs|node version number>
    --lts                                   When installing, only select from LTS (long-term support) versions
    --lts=<LTS name>                        When installing, only select from versions for a specific LTS line
    --skip-default-packages                 When installing, skip the default-packages file if it exists
    --latest-npm                            After installing, attempt to upgrade to the latest working npm on the given node version
  nvm uninstall <version>                   Uninstall a version
  nvm uninstall --lts                       Uninstall using automatic LTS (long-term support) alias `lts/*`, if available.
  nvm uninstall --lts=<LTS name>            Uninstall using automatic alias for provided LTS line, if available.
  nvm use [--silent] <version>              Modify PATH to use <version>. Uses .nvmrc if available
    --lts                                   Uses automatic LTS (long-term support) alias `lts/*`, if available.
    --lts=<LTS name>                        Uses automatic alias for provided LTS line, if available.
  nvm exec [--silent] <version> [<command>] Run <command> on <version>. Uses .nvmrc if available
    --lts                                   Uses automatic LTS (long-term support) alias `lts/*`, if available.
    --lts=<LTS name>                        Uses automatic alias for provided LTS line, if available.
  nvm run [--silent] <version> [<args>]     Run `node` on <version> with <args> as arguments. Uses .nvmrc if available
    --lts                                   Uses automatic LTS (long-term support) alias `lts/*`, if available.
    --lts=<LTS name>                        Uses automatic alias for provided LTS line, if available.
  nvm current                               Display currently activated version
  nvm ls                                    List installed versions
  nvm ls <version>                          List versions matching a given <version>
  nvm ls-remote                             List remote versions available for install
    --lts                                   When listing, only show LTS (long-term support) versions
  nvm ls-remote <version>                   List remote versions available for install, matching a given <version>
    --lts                                   When listing, only show LTS (long-term support) versions
    --lts=<LTS name>                        When listing, only show versions for a specific LTS line
  nvm version <version>                     Resolve the given description to a single local version
  nvm version-remote <version>              Resolve the given description to a single remote version
    --lts                                   When listing, only select from LTS (long-term support) versions
    --lts=<LTS name>                        When listing, only select from versions for a specific LTS line
  nvm deactivate                            Undo effects of `nvm` on current shell
  nvm alias [<pattern>]                     Show all aliases beginning with <pattern>
  nvm alias <name> <version>                Set an alias named <name> pointing to <version>
  nvm unalias <name>                        Deletes the alias named <name>
  nvm install-latest-npm                    Attempt to upgrade to the latest working `npm` on the current node version
  nvm reinstall-packages <version>          Reinstall global `npm` packages contained in <version> to current version
  nvm unload                                Unload `nvm` from shell
  nvm which [<version>]                     Display path to installed node version. Uses .nvmrc if available
  nvm cache dir                             Display path to the cache directory for nvm
  nvm cache clear                           Empty cache directory for nvm

Example:
  nvm install 8.0.0                     Install a specific version number
  nvm use 8.0                           Use the latest available 8.0.x release
  nvm run 6.10.3 app.js                 Run app.js using node 6.10.3
  nvm exec 4.8.3 node app.js            Run `node app.js` with the PATH pointing to node 4.8.3
  nvm alias default 8.1.0               Set default node version on a shell
  nvm alias default node                Always default to the latest available node version on a shell

Note:
  to remove, delete, or uninstall nvm - just remove the `$NVM_DIR` folder (usually `~/.nvm`)
```
