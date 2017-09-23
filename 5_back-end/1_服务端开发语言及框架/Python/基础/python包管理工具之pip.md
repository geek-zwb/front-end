## What ?
> The PyPA recommended tool for installing Python packages.

## Install
在 Python 2 >= 2.7.9 或 Python 3 >= 3.4 的版本里已经默认安装了 pip。You just need to upgrade it.

install
```
python get-pip.py
```

我使用的 mac， 系统自带了 python 2.7, 上面安装失败

使用 homebrew 安装
```
# homebrew 是 mac 下用于安装软件的工具，如果没有 homebrew, 安装方法如下
# /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

brew install pip
```

still error ? try:
```
sudo easy_install pip
```

## upgrading
```
pip install -U pip
```

