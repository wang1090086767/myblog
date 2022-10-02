# Mac Brew 记录

## brew安装与卸载

> 官网下载安装：[brew](https://brew.sh/)

### 安装

``` bash
  /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### 卸载

``` bash
  /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```

## brew换源

操作步骤：

   ``` bash
   #1. 到brew 的工作目录
   cd "$(brew --repo)"
   #2. 更换brew源
   git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
   #3. 到homebrew-core工作目录
   cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
   #4. 更换homebrew-core源
   cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
   #5. 更新
   brew update
   ```

## brew 路径记录

mac系统中brew下载路径: `用户/用户名/资源库/Caches/Homebrew/downloads/`

brew安装路径:`/usr/local/Cellar/`
