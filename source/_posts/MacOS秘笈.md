---
title: MacOS秘笈
date: 2022-12-29 15:43:42
tags: 收集
---

### 1.修复‘xxx.app已损坏无法打开，你应该将它移到废纸篓’

##### 安全隐私任何来源

``` bash
$ sudo spctl --master-disable
```

##### 绕过公证

``` bash
$ sudo xattr -rd com.apple.quarantine /Applications/xxx.app
```


### 2.修复10.15「意外退出」及「崩溃闪退」问题

##### 安装Command Line Tools 工具

``` bash
$ xcode-select --install
```

##### 签名

``` bash
$ sudo codesign --force --deep --sign - /Applications/xxx.app
```

##### 若报错

``` bash
$ xattr -cr /Applications/xxx.app
```

再签名



### 3.home-brew安装

``` bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```



### 4.调整Launchpad图标大小

``` bash
$ defaults write com.apple.dock springboard-rows -int 6
$ defaults write com.apple.dock springboard-columns -int 8
$ killall Dock
```

恢复

``` bash
$ defaults write com.apple.dock springboard-rows Default
$ defaults write com.apple.dock springboard-columns Default
$ killall Dock
```



### 5.隐藏桌面文件

``` bash
$ defaults write com.apple.finder CreateDesktop -bool false;killall Finder
```

恢复显示

``` bash
$ defaults write com.apple.finder CreateDesktop -bool true;killall Finder
```

