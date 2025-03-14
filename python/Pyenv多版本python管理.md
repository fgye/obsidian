
---
### **📌 `pyenv` 常用指令**

`pyenv` 主要用于管理多个 Python 版本，以下是一些常用命令：

| 命令                    | 作用                       |
| --------------------- | ------------------------ |
| pyenv install --list  | 查看可安装的 Python 版本         |
| pyenv install <版本号>   | 安装指定版本的 Python           |
| pyenv versions        | 查看已安装的 Python 版本         |
| pyenv global <版本号>    | 设置全局默认 Python 版本         |
| pyenv local <版本号>     | 仅对当前目录（项目）使用特定 Python 版本 |
| pyenv shell <版本号>     | 仅对当前终端会话生效               |
| pyenv uninstall <版本号> | 卸载某个 Python 版本           |
| pyenv rehash          | 重新生成 `shims`，通常在安装新版本后使用 |
| pyenv which python    | 显示当前使用的 Python 解释器路径     |
| pyenv doctor          | 检查 `pyenv` 是否正常工作        |

```
# `pyenv` 常用指令

## 1. 查看 `pyenv` 版本
```bash
pyenv --version

```
# 列出可安装的 Python 版本

```
pyenv install --list

```

# 安装指定 Python 版本

```
pyenv install 3.9.6

```

你也可以安装多个版本：
```
pyenv install 3.8.10
pyenv install 3.10.2
```

# 查看已安装的 Python 版本

```
pyenv versions

```

输出示例：

```
  system
  3.8.10
* 3.9.6 (set by /home/user/.pyenv/version)
  3.10.2
星号（`*`）表示当前正在使用的 Python 版本。
```

# 切换 Python 版本

全局设置默认 Python 版本
```
pyenv global 3.9.6

```

**只在当前目录使用特定 Python 版本**：
```
pyenv local 3.8.10

```

**只在当前 shell 会话中临时使用某个版本**：
```
pyenv shell 3.10.2

```

# 更新 `pyenv`

```
pyenv update

```

# `pyenv` 走代理安装 Python

使用 `https_proxy` 和 `http_proxy`

```
export http_proxy="http://192.168.43.219:7897"

export https_proxy="http://192.168.43.219:7897"

pyenv install 3.9.6

```
# 使用 `git` 代理

`pyenv` 需要通过 `git` 下载资源，可以为 `git` 配置代理：

```
git config --global http.proxy "http://192.168.43.219:7897"
git config --global https.proxy "http://192.168.43.219:7897"
```
取消代理
```
git config --global --unset http.proxy
git config --global --unset https.proxy
```
使用国内镜像加速
```
export PYTHON_BUILD_MIRROR_URL="https://mirrors.tuna.tsinghua.edu.cn/pyenv/"
pyenv install 3.9.6
```

# pip源

```
export PIP_INDEX_URL=https://pypi.tuna.tsinghua.edu.cn/simple

pip install -i https://pypi.tuna.tsinghua.edu.cn/simple requests
```

命令

```
chsh -s $(which zsh)
zsh
```