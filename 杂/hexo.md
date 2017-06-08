--------------------
title: 使用hexo搭建博客
tags: hexo
--------------------
# 基本环境安装
## git
```
sudo apt-get install git
git config --global user.name "omsfuk"
git config --golbal user.email "omsfuk@163.com"
生成密钥
ssh-keygen -t rsa -C "omsfuk@163.com"
将id_rsa.pub文件里的内容设置到github中
```
## nodejs
```
wget  http://nodejs.org/dist/v4.5.0/node-v4.5.0-linux-x64.tar.xz
tar -vxf node-v4.5.0-linux-x64.tar.xz
```

然后将文件复制过去
```
sudo cp -r bin/* /usr/local/bin/
sudo cp -r lib/* /usr/local/lib/
sudo cp -r include/* /usr/local/include/
```

查看node版本
```
node -v
```
## npm
```
sudo apt-get install npm
```
## hexo
```
sudo npm install hexo-cli -g
```

# 基本命令
```
hexo init 初始化。
hexo new [layout] <title> 创建新文章。生成的文件会在source/_post/目录下，直接编辑就好
hexo clean 清理缓存，当经常更换主题或发表文章时使用
hexo g 生成静态网页
hexo d 部署
hexo s 启动服务器
```

# 更换主题
直接下载主题，然后放在themes目录下，在`_config.yml`中更改主题名

