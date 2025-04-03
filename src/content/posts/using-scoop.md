---
title: scoop
published: 2025-04-03
description: scoop 的安装教程和配置
image: ''
tags: [Windows, ]
category: Shell
draft: false 
lang: ''
---

## 1. 在 PowerShell 中打开远程权限  

```Powershell
Set-ExecutionPolicy RemoteSigned -scope CurrentUser
```

:::warning
Powershell 脚本运行的几种权限：
* Restricted: 默认设置，不允许任何脚本运行
* AllSigned: 只能运行经过数字证书签名的脚本
* RemoteSigned: 运行本地脚本不需要数字签名，但是运行从网络上下载的脚本必须要有数字签名
* Unrestricted: 允许所有的脚本运行, 但是在运行前会提示是否进行操作
* Bypass: 允许所有的脚本运行, 没有任何的提示和警告。

修改回默认设置

```Powershell
Set-ExecutionPolicy Restricted -scope CurrentUser
```

:::

## 2. 下载安装脚本

```Powershell
irm get.scoop.sh -outfile 'install.ps1'
```

:::tip
或者使用国内镜像安装

```Powershell
irm scoop.201704.xyz -outfile 'install.ps1'
```

:::

## 3. 自定义安装位置

:::important
scoop默认安装位置在: C:\Users\username\scoop
:::

```Powershell
.\install.ps1 -ScoopDir 'Scoop_Path' -ScoopGlobalDir 'GlobalScoop_Path' -Proxy 'http://<ip:port>'
```

成功后执行:

```Powershell
scoop update
```

:::tip
切换到国内的源

```Powershell
scoop config SCOOP_REPO https://gitee.com/glsnames/scoop-installer
```

:::

## 4. 添加bucket

可以使用进行查看默认的已知bucket

```Powershell
scoop bucket known
```

添加需要的bucket

```Powershell
scoop bucket add main
scoop bucket add versions
scoop bucket add extras
scoop bucket add scoop-cn https://github.com/duzyn/scoop-cn
```

:::tip
其他bucket 可在该连接中查看 [scoop-directory](https://rasa.github.io/scoop-directory/by-score.html)  
其他bucket 查看网址: [scoop.netlify.app](https://scoop.netlify.app/buckets/)  
如果仓库源无法访问, 可以走GitHub的代理或者使用国内的源  
:::

## 5. 软件安装

```Powershell
scoop install uv
scoop install python312
scoop install fzf

scoop install nodejs
```

## 6. 配置备份

在重装系统后, 多设备同步或其他情况下需要保持环境一致

### 6.1 导出为json文件

```Powershell
scoop export > scoop.json​
```

### 6.2 从json文件导入

```Powershell
scoop import .\scoop.json
```

## 7. 相关命令

### 更新

```Powershell
scoop update
或是
scoop update *
```

### 卸载

```Powershell
scoop uninstall <name>
```

### 切换到指定版本

```Powershell
scoop reset <name@version>
```

### 清除缓存

```Powershell
scoop cache rm *
scoop cleanup rm *
```

### 清除旧软件版本

不删除会一直占用空间

```Powershell
scoop cleanup rm *
```

## 参考

> https://zhuanlan.zhihu.com/p/128955118  
> https://gitee.com/scoop-installer/scoop  
> https://muxiner.github.io/using-scoop/  
