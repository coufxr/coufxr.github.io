---
title: 在wsl中安装ArchLinux
published: 2025-05-07
description: '在wsl中安装ArchLinux'
image: ''
tags: [Windows, ArchLinux]
category: 'wsl'
draft: false 
lang: ''
---

> [! TIPS]  
> ArchLinux现已官方支持wsl  
> wsl也已官方支持ArchLinux  

## 安装wsl

用管理员打开powershell输入

### 1. 启用WSL

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

### 2. 启用虚拟平台

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

### 3. 下载Linux内核升级包

> 下载地址：<https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi>

wsl更新至最新

```powershell
wsl.exe --update
```

### 4. 将WSL2设置为默认版本

```powershell
wsl --set-default-version 2
```

## 安装ArchLinux

查看所有Linux版本

```powershell
wsl --list --online
```

安装arch

```powershell
wsl --install -d ArchLinux
```

> --location <path> 设置安装位置  
> --name <name> 设置安装名称

## 配置ArchLinux

### 进入系统

```powershell
wsl -d ArchLinux
```

### 网络配置

删除网络连接配置

```bash
rm /etc/resolv.conf
```

重新启动 `Archlinux`

> exit

然后再次进入 `Archlinux`

### 换源

```bash
cd /etc/
explorer.exe .
```

文件管理器中找到 `pacman.conf` ，在这个文件最后加入

```text
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

然后进入下一级目录 `pacman.d`

编辑里面的 `mirrolist` 文件，将China的源注释去掉（选择部分即可）

### 安装包

问题:

> error: archlinuxcn-keyring: Signature from "Jiachen YANG (Arch Linux Packager Signing Key) " is marginal trust  
> <https://www.archlinuxcn.org/archlinuxcn-keyring-manually-trust-farseerfc-key/>  
> `pacman-key --lsign-key "farseerfc@archlinux.org"`

```bash
pacman -Syy
pacman-key --init
pacman-key --populate
pacman -S archlinuxcn-keyring
pacman -S base base-devel vim git wget
```

#### 其他推荐

建议安装:

* `paru` 基于 Yay 的新 AUR 助手: <https://github.com/Morganamilo/paru>
* `zsh` (chsh -s /usr/bin/zsh)
* `eza` 现代的 `ls` 替代品: <https://github.com/eza-community/eza>
* `bat` 类似 cat，但带有 git 集成和语法高亮: <https://github.com/sharkdp/bat>

可选择:

* `bottom` 用于终端的可定制的跨平台图形化进程/系统监视器: <https://github.com/ClementTsang/bottom>
* `procs` 现代的 `ps` 替代品: <https://github.com/dalance/procs>
* `neofetch` 命令行系统信息工具: <https://github.com/dylanaraps/neofetch>
* `yazi` 极速终端文件管理器: <https://github.com/sxyazi/yazi>
* `fzf` 通用的命令行模糊查找器 <https://github.com/junegunn/fzf>
* `kubectx` 在kubectl集群之间切换的工具 <https://github.com/ahmetb/kubectx>
* `uv` Rust编写Python包和项目管理器 <https://github.com/astral-sh/uv>

终端美化:

* `starship` 轻量、迅速、客制化的高颜值终端: <https://starship.rs/zh-CN/>
* `sheldon` rust编写的zsh插件管理器: <https://github.com/rossmacarthur/sheldon>

全部安装:

```shell
pacman -S paru zsh eza bat bottom procs neofetch yazi fzf starship sheldon
```

#### 相关配置

相关配置存放在.config文件夹下

paru 中启用颜色，你必须先在 pacman 中启用它: 去除 `Color` 前的 (#)

```bash
vim /etc/pacman.conf
```

### 新建账户

给当前的root设置密码

```bash
passwd
```

然后新建一个普通用户

```bash
useradd -m -G wheel -s /bin/bash <用户名>
passwd <用户名>
```

#### 权限

打开文件 `sudoers`

```bash
vim /etc/sudoers
```

取消 `%wheel ALL=(ALL) ALL` 的注释

到此为止已经完成了 `Archlinux` 的配置

### 设置 Locale

1. 去掉 en_US.UTF-8 UTF-8 以及 zh_CN.UTF-8 UTF-8 行前的注释符号（#）

```shell
vim /etc/locale.gen
```

2. 然后使用如下命令生成 locale

```shell
locale-gen
```

3. 向 /etc/locale.conf 输入内容：

```shell
echo 'LANG=en_US.UTF-8'  > /etc/locale.conf
```

中文字体可选安装

## 重要

### 登录用户

`wsl` 会默认以 `root` 用户登录 `Archlinux` ，所以需要修改 `Archlinux` 的默认用户

需要在 `/etc/wsl.conf` 中添加以下内容:

```text
# Set the user when launching a distribution with WSL.
[boot]
systemd=true

[user]
default = <用户名>
```

### 终端进入wsl

终端默认生成的archLinux 配置文件中的命令行 **不建议修改**
可以缩短wsl路径, 但不建议删除 `--distribution-id` 配置

可在注册表中找到:

```path
\HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Lxss
```

路径下是所有的wsl镜像配置

### systemd 相关

> [! TIPS]  
> WSL版本 > 2.4.12 则不需要

<https://wiki.archlinux.org/title/Install_Arch_Linux_on_WSL#systemd_requires_plain_cgroup_v2_support>

在 %USERPROFILE%/.wslconfig 中添加

```text
[wsl2]
kernelCommandLine = cgroup_no_v1=all systemd.unified_cgroup_hierarchy=1
```

`wsl.conf` 的其他配置:

> <https://learn.microsoft.com/zh-cn/windows/wsl/wsl-config>

### 其他

* arch_logo: <https://cdn0.iconfinder.com/data/icons/flat-round-system/512/archlinux-512.png>

* zi: <https://blog.maple3142.net/2020/09/27/zsh-customization/>
* zinit: <https://www.cnblogs.com/hongdada/p/16821715.html>

## 参考

> <https://wiki.archlinux.org/title/Install_Arch_Linux_on_WSL>  
> <https://zhuanlan.zhihu.com/p/266585727>  
> <https://arch.icekylin.online/guide/rookie/basic-install.html#_14-%E8%AE%BE%E7%BD%AE-locale>  
> <https://itsfoss.com/paru-aur-helper/>  
