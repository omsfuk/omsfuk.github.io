# Minecraft服务器架设教程

## 服务端是啥

服务端是用来开Minecraft服务器的，可以放到VPS上，大家连到这个VPS上，然后只要联网，就可以联机玩MC了。
选择正确的服务端能有效提升服务器性能
比如主流服务端中优化最好的是Paper（PaperSpigot）

## 服务端分类
#### 原版服务端
* Minecraft官方服务端
* CraftBukkit（水桶服）
* Spigot（水龙头服）
* Paper（原名PaperSpigot）

#### Mod服务端
* Forge Server
* KCauldron（原名Cauldron，MCPC+）
* Sponge（海绵）

## 不同服务器介绍
#### 官服
* 保留最根本的运算机制（红石电路和原版的算法相同），不像Paper为了性能优化而魔改红石机制
* 无法使用插件
* 无法使用Mod
* 自带控制界面（其他服务端都没有，貌似只能看，要“控制”还是要到黑窗口敲命令）。可以加上nogui取消显示gui。例如`java .\minecraft_server_1.7.10.jar nogui`
* 给官服打上Forge就可以装mod

#### CraftBukkit
* 比官服更优化，效率更高
* 曾经是用户最多的服务端（现在已经被Spigot超越）
* 支持插件，适合开一个大型服务器
* 不支持Mod
* 没有管理界面，只有控制台，新腐竹需要学习一段时间
*
#### Spigot
* 用户最多
* 自带反X-Ray功能（推荐开启）
* 随着做弊端的普及，反作弊成为了一个服务器必备的功能
* 比CB更优化，效率更高
* 可有效分配利用资源，人多的服效果更为明显
* 理论支持所有插件√


#### PaperSpigot
* 1.9以下的叫PaperSpigot，1.9之后为了钻法律的漏洞改名叫Paper，又称PaperClip
* 极致优化，性能和效率超越了Spigot
* 1G内存带起来十几个人一点问题也没有
* TNT不再卡服
* 高空流水不再卡服
* 红石算法优化（魔改）
* 区块压缩节约内存
* 反X-Ray优化
* 更多自定义魔改项目

#### Forge Server
* 支持Mod
* 不支持插件
* Mod会很降低服务器的效率，会让服务器更卡
* 没有优化

#### Cauldron
* 支持插件（有些插件不兼容）
* 支持Mod（有些Mod不兼容）
* 比Forge Server优化好
* 不支持1.7.10以上的版本
* 因为它是基于BC的，所以也被DMCA干掉了

## 服务器安装
#### 官方服务器 + Forge
* 下载官服 [https://s3.amazonaws.com/Minecraft.Download/versions/1.7.10/minecraft_server.1.7.10.jar](`https://s3.amazonaws.com/Minecraft.Download/versions/1.7.10/minecraft_server.1.7.10.jar`)
可以把链接中的版本号改一改，会下载对应的官方服务器

* 运行`minecraft_server.1.7.10.jar`
`java -jar minecraft_server.1.7.10.jar nogui`我选择无gui模式运行
此时会运行失败，因为你需要选择同意一个协议。
在目录下有个eula.txt，将其中的`eula=false`改为`eula=true`即代表同意协议。再次启动，即可成功。
但是不要启动，因为没什么作用，接下来要对它加装Forge，使它支持mod

* 下载对应版本的 [Windows Forge Installer](https://files.minecraftforge.net/)
选择minecraft_server.jar所在的目录，安装，等待，然后搞定。

* 运行forge-1.7.10-10.13.4.1614-1.7.10-universal
`java -jar forge-1.7.10-10.13.4.1614-1.7.10-universal.jar nogui`，它会启动带有Forge的`minecraf\_server`。以后只需要执行这条命令就好了，不需要直接启动官方的`minecraft_server.jar`
同时会产生mods文件夹，以后将mod放在此文件夹下就好了。

* **只需要放增加了新方块的mod，其他mod不要乱放，否则可能会导致无法启动**
