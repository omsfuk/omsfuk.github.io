------------
title: ubuntu初始化&&美化
tags: ubuntu
------------
[TOC]
# Ubuntu初始化以及美化

## 系统版本
* Ubuntu 16.10
可以安装screenfetch来查看系统信息
```
sudo apt-get install screenfetch
screenfetch
```

## git
```
sudo apt-get install git
```

## vim
```
sudo apt-get install vim
```

## JDK
* openjdk

```
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt-get install openjdk-7-jdk
```

* oraclejdk

```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update

jdk7
sudo apt-get install oracle-java7-installer
jdk8
sudo apt-get install oracle-java8-installer
```

## Chorme浏览器
```bash
# 获取deb安装包
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
# 安装
sudo dpkg -i google-chrome-stable_current_amd64.deb
```

## 网易云音乐
直接去官网下载deb包，安装就好了

## mysql
```
sudo apt-get install mysql-server
```

## 翻墙
去laod博客下载hosts文件，然后敲
```
# 重启网络管理器
sudo systemctl restart NetworkManager
```
或者关闭网络连接再打开。
google访问要加`https`

## bcloud
直接去github下载deb包就可以了。然后安装

如果报错，获取bdstoken失败，参考
```
http://bbs.deepin.org/forum.php?mod=viewthread&tid=43445
```

* Step 1:
```
sudo gedit /usr/lib/python3/dist-packages/bcloud/auth.py
在get_bdstoken函数的if req:前一行添加"cookie.load_list(req.headers.get_all('Set-Cookie'))"
```

* Step 2:
```
sudo gedit /usr/lib/python3/dist-packages/bcloud/pcs.py
把所有cookie.sub_output()的参数添加上'SCRC','STOKEN'
例：'Cookie': cookie.sub_output('BAIDUID', 'BDUSS', 'PANWEB', 'cflag', 'SCRC', 'STOKEN'),
只需要修改4个参数的函数
```

* Step 3
```
sudo rm -rf ~/.config/bcloud/*
sudo rm -rf ~/.cache/bcloud/*
```

* Step 4
重新运行Bcloud

## wps

* 首先卸载libreoffice
```
sudo apt-get -y purge libreoffice*
```
zsh貌似不兼容，需要切换到bash
```
/bin/sh
sudo apt-get -y purge libreoffice*
```
* 安装wps
去官网下载wpf for linux，直接安装就好了

启动WPS for Linux后，出现提示"系统缺失字体" 。

出现提示的原因是因为WPS for Linux没有自带windows的字体，只要在Linux系统中加载字体即可。

具体操作步骤如下：

 1. 下载缺失的字体文件，然后复制到Linux系统中的/usr/share/fonts文件夹中。

国外下载地址：`https://www.dropbox.com/s/lfy4hvq95ilwyw5/wps_symbol_fonts.zip`

国内下载地址：'http://pan.baidu.com/s/1mh0lcbY`

下载完成后，解压并进入目录中，继续执行：
```
sudo cp *.ttf *.TTF /usr/share/fonts
```

 2. 执行以下命令,生成字体的索引信息：
```
sudo mkfontscale
sudo mkfontdir
```

 3. 运行fc-cache命令更新字体缓存。
```
sudo fc-cache
```

 4. 重启wps即可，字体缺失的提示不再出现。
（生成的文件可以删除，貌似。。）

## QQ intl
#### 下载地址
下载地址: http://linux.linuxidc.com/

用户名与密码: www.linuxidc.com

具体下载目录在 /2016年资料/9月/6日/Ubuntu 16.04安装QQ国际版图文详细教程/

#### 安装依赖库
```
sudo apt-get install libgtk2.0-0:i386
sudo apt-get install lib32ncurses5
```

#### 解压并安装wineqqintl
```
tar -vxf wine-qqintl-www.linuxidc.com.tar.xz
sudo dpkg -i wine-qqintl_0.1.3-2_i386.deb
sudo dpkg -i ttf-wqy-microhei_0.2.0-beta-2_all.deb
sudo dpkg -i fonts-wqy-microhei_0.2.0-beta-2_all.deb
```
#### 关闭手机上的设备锁，否则会提示版本过低

## zsh
```
# 安装zsh
sudo apt-get install zsh
# 设为默认登录shell，可以查看/etc/passwd验证是否更改
chsh -s /bin/zsh
```

## Oh My ZSH!
Oh My Zsh是一款社区驱动的命令行工具，正如它的主页上说的，Oh My Zsh 是一种生活方式。它基于zsh命令行，提供了主题配置，插件机制，已经内置的便捷操作。给我们一种全新的方式使用命令行。

Oh My Zsh这个名字听起来就很有意思，它是基于zsh命令行的一个扩展工具集，提供了丰富的扩展功能。

Oh My Zsh只是一个对zsh命令行环境的配置包装框架，但它不提供命令行窗口，更不是一个独立的APP。
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
配置主题，可以修改`.zshrc`的`ZSH_THEME`来选择主题。具体主题可以查看`oh my zsh`官网说明。感觉默认的就挺好。

然后在终端上右键，更改配置文件，来个透明效果

## Unity Tweak Tool
网上找的ppa都失效了，直接去软件中心下载就好了。搜索`unity-tweak-tool`，一个字符也不要错。
下载安装。ok

## OSX - Arc
去Linux公社下载`OSX - Arc`主题
地址`http://linux.linuxidc.com/`,用户名与密码都是`www.linuxidc.com`
具体下载目录在`/2017年资料/1月/1日/GTK3主题：OSX-Arc/`
将提取后的文件复制到`/usr/share/themes`

借助Unity Tweak Tool更换主题为`OSX - Arc - darker`

## Numix图标
```
sudo add-apt-repository ppa:numix/ppa
sudo apt-get update
sudo apt-get install numix-icon-theme
sudo apt-get install numix-icon-theme-circle # 圆形图标比较美观
```

## 更改用户文件夹
我的安装完是中文的，感觉不爽，
```
vim .config/user-dirs.dirs

# 改一该就好了
XDG_DESKTOP_DIR="$HOME/Desktop"
XDG_DOWNLOAD_DIR="$HOME/Downloads"
XDG_TEMPLATES_DIR="$HOME/Templates"
XDG_PUBLICSHARE_DIR="$HOME/Public"
XDG_DOCUMENTS_DIR="$HOME/Documents"
XDG_MUSIC_DIR="$HOME/Music"
XDG_PICTURES_DIR="$HOME/Pictures"
XDG_VIDEOS_DIR="$HOME/Videos"

```
然后手动将中文名改为英文的
