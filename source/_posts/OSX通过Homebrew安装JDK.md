---
title: OSX通过Homebrew安装JDK
date: 2017-04-13 09:17:51
tags: 
 - OSX
 - Mac
 - Homebrew
 - JDK
---
# 安装Homebrew
```bash
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

# 安装Homebrew Cask
```bash
$ brew install caskroom/cask/brew-cask
```
应用也可以通过 App Store 安装，而且有些应用只能通过 App Store 安装，比如 Xcode 等一些 Apple 的应用。App Store 没有对应的命令行工具，还需要 Apple ID。倒是更新起来很方便。

几乎所有常用的应用都可以通过 brew-cask 安装，而且是从应用的官网上下载，所以你要安装新的应用时，建议用 brew-cask 安装。如果你不知道应用在 brew-cask 中的 ID，可以先用<code>brew cask search</code>命令搜索

# 安装JDK
```bash
$ brew cask install java
```

# 如果提示已经安装，则使用
```bash
$ brew cask reinstall java
```

# 如果你需要安装指定版本，可以使用`homebrew-cask-versions`
```bash
$ brew cask reinstall java
```
brew tap caskroom/versions
brew cask install java8
# 测试JDK是否正确安装
```bash
$ java -version
```