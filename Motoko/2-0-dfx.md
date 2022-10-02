# 2.0 了解和安装 DFX, Vessel 是什么

## 安装 DFX

### macos

`sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"`

### Windows

首先安装: WSL 2 或使用 Ubuntu , 然后运行 `sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"`

安装指定的 dfx 版本: `DFX_VERSION=0.11.1 sh -ci "$(curl -sSL https://internetcomputer.org/install.sh)"`

### 安装 nodejs

## 安装后目录

dfx 可执行文件目录, `/usr/local/bin/dfx`

moc 用来调试 motoko 的命令行版本 `~/.cache/dfinity/versions/<VERSION>/moc`

replica 缓存的本地开发复制副本 `~/.cache/dfinity/versions/<VERSION>/replica`

uninstall 卸载 dfx `~/.cache/dfinity/uninstall.sh`

彻底清除和删除 dfx, `~/.cache/dfinity/uninstall.sh && sh -ci "$(curl -sSL https://internetcomputer.org/install.sh)"`

version 当前安装的版本 `~/.cache/dfinity/versions`

设置 dfx 的版本=》dfx.json

## 什么是 Vessel

Motoko 的包管理工具, 可以理解为不同的语言有不同的包同步工具,方便维护代码和代码的简洁性.

代码地址: `https://github.com/dfinity/vessel`

### 安装 Vessel

首先访问和下载 vessel 的打包后的文件, `https://github.com/dfinity/vessel/releases`

不同的环境可以选择不同的文件进行下载, 比如 macOS、win、linux 等

如果你使用 Ubuntu 切换到 `$HOME/bin` 目录,并执行 `wget https://github.com/dfinity/vessel/releases/download/v0.6.4/vessel-linux64`

如果你使用 macOS, 切换到目录 /usr/local/bin 执行命令: `wget https://github.com/dfinity/vessel/releases/download/v0.6.4/vessel-macos`

修改文件名: `mv vessel-linux64 vessel` or `mv vessel-macos vessel`

并给它可执行权限 : `chmod +x vessel`

### 如何使用

可以直接在你的项目内运行: `vessel init` 进行初始化

如果添加其它包,可以直接编辑文件 `vessel.dhall`

查看你的 `dfx.json` 并添加 vessel 的支持.

```
"defaults": {
  "build": {
    "packtool": "vessel sources"
  }
}
```

然后运行 `dfx build` 即可拉取需要的包
