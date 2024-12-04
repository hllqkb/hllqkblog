---
abbrlink: ''
categories: []
cover: https://cdn.hllqk.cn/2024/12/7e69e81090cd9d6749ae281cdd07b501.png
date: '2024-11-26T17:38:51.016099+08:00'
tags: []
title: VMware 安装配置 Ubuntu（最新版、超详细）
updated: '2024-11-26T17:38:54.536+08:00'
---
> **Linux 系列教程：**
>
> * [VMware 安装配置 Ubuntu（最新版、超详细）](https://blog.csdn.net/m0_70885101/article/details/137694608)
> * [FinalShell 远程连接 Linux（Ubuntu）系统](https://blog.csdn.net/m0_70885101/article/details/137866944)
> * [Ubuntu 系统安装 VS Code 并配置 C++ 环境](https://blog.csdn.net/m0_70885101/article/details/137871474)

#### 文章目录

* [1. 下载安装 VMware](https://blog.csdn.net/m0_70885101/article/details/137694608#1__VMware_7)
* [2. 检查网络适配器](https://blog.csdn.net/m0_70885101/article/details/137694608#2__30)
* [3. Ubuntu 下载](https://blog.csdn.net/m0_70885101/article/details/137694608#3_Ubuntu__56)
* [4. 创建虚拟机](https://blog.csdn.net/m0_70885101/article/details/137694608#4__79)
* [5. 开启虚拟机](https://blog.csdn.net/m0_70885101/article/details/137694608#5__94)
* [6. 安装 VWware Tools](https://blog.csdn.net/m0_70885101/article/details/137694608#6__VWware_Tools_121)

## 1. 下载安装 **VMware**

> ➡️➡️➡️来源：[VMware Docs](https://docs.vmware.com/cn/VMware-Workstation-Pro/17.5/rn/vmware-workstation-175-pro-release-notes/index.html)

> VMware Workstation Pro™ 使专业技术人员能够在同一台 PC 上同时运行多个基于 x86 的 **Windows**、Linux 和其他操作系统，从而开发、测试、演示和部署软件。

![](https://i-blog.csdnimg.cn/blog_migrate/5b478c7fed8adab399b077f19d46356a.png)

**[Step 1]：** 点击 [VMware Workstation 17 Pro 下载](https://www.vmware.com/content/vmware/vmware-published-sites/us/products/workstation-pro/workstation-pro-evaluation.html.html) 页面中的 `DOWNLOAD NOW`，即开始下载 `VMware-workstation-full-17.5.1-23298084.exe`

![](https://i-blog.csdnimg.cn/blog_migrate/24b5af607e082aa850baa869f3896a83.png)

**[Step 2]：** 双击运行 `.exe` 文件，一直默认安装即可（建议安装在非C/系统盘），并输入以下许可证密钥

```
MC60H-DWHD5-H80U9-6V85M-8280D
```

![](https://i-blog.csdnimg.cn/blog_migrate/3c959f1928beaf2ee922373ce60abba2.png)

## 2. 检查网络适配器

**注意（Look me, look me…）：**

[VMware 安装](https://so.csdn.net/so/search?q=VMware%20%E5%AE%89%E8%A3%85&spm=1001.2101.3001.7020)完成后，需要确认自己的电脑是否已安装两个网络适配器：`VMware Network Adapter VMnet1` 和 `VMware Network Adapter VMnet8`，否则会影响**虚拟机**的联网功能。

以下三种方式可以查看到网络适配器：

**[Way 1]：**`Windows + R` 输入 `cmd` 调出窗口，输入 `ipconfig` 查看网络配置

![](https://i-blog.csdnimg.cn/blog_migrate/6face56719dc7bbec7c7428bd8704ba2.png)

**[Way 2]：**`Windows + i` 调出设置 ➡️ 网络和 Internet ➡️ 高级网络设置 ➡️ 网络适配器

![](https://i-blog.csdnimg.cn/blog_migrate/ff751da5d50a5d3720001301689af36e.png)

**[Way 3]：** 右键 `此电脑` ➡️ 管理 ➡️ 计算机管理 ➡️ 系统工具 ➡️ 设备管理器 ➡️ 网络适配器

![](https://i-blog.csdnimg.cn/blog_migrate/7151ac9b87a256c2437cf866c049ef48.png)

> 如果在 VMware 安装完成后，并没有通过上述三种方式找到 `VMnet1` 和 `VMnet8` 网络适配器，请参考 [完美解决VMware安装后没有VMnet1和VMnet8的问题](https://blog.csdn.net/KRISNAT/article/details/124107032)。

## 3. **Ubuntu** 下载

> ➡️➡️➡️来源：[Wikipedia](https://en.wikipedia.org/wiki/Ubuntu)

> Ubuntu 是一个基于 Debian 的 **Linux** 发行版，主要由免费和[开源软件](https://edu.csdn.net/cloud/pm_summit?utm_source=blogglc)组成。该操作系统由英国公司 Canonical 和其他开发者社区在精英管理模式下开发。
>
> 截至2023年10月，最新版本为23.10（“Mantic牛头怪”），当前长期支持版本为22.04（“海哲明水母”）。

![](https://i-blog.csdnimg.cn/blog_migrate/ee1c27a78722456616e98d2be1c8c2ce.png)

**[Download - 官网]：** 点击 [Ubuntu 22.04.4 LTS 下载](https://ubuntu.com/download/desktop) 页面中的 `Download 22.04.4`，即开始下载 `ubuntu-22.04.4-desktop-amd64.iso`

![](https://i-blog.csdnimg.cn/blog_migrate/788333c22fb6ef3d523cafef06f280bc.png)

> 最新版 Ubuntu 22.04.4 LTS 大小为 5.02 GB，由于官网是从国外服务器下载，速度比较慢，这里推荐另外一种方法。

**[Download - 清华镜像站]：** 点击 [清华大学开源软件镜像站 - Ubuntu 22.04.4 下载](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/22.04.4/) 页面中的 `ubuntu-22.04.4-desktop-amd64.iso`

![](https://i-blog.csdnimg.cn/blog_migrate/d1bd6461b37c0a6b92377e8333054e99.png)

## 4. 创建虚拟机

**[Step 1]：** 打开 VMware 软件，点击 `创建新的虚拟机`

![](https://i-blog.csdnimg.cn/blog_migrate/7b0a76d008fa093ceaba2ef8b55e8161.png)

**[Step 2]：** 新建虚拟机向导如下图

![](https://i-blog.csdnimg.cn/blog_migrate/a27a9b57782c309165b2d17f0ee4ca34.png)

![](https://i-blog.csdnimg.cn/blog_migrate/8e7bd8e4680f0c76beb8abbb0bcbb599.png)

## 5. 开启虚拟机

**[Step 1]：** 打开 VMware 软件，即可看到新建的虚拟机 Ubuntu 64位，点击 `开启此虚拟机`

![](https://i-blog.csdnimg.cn/blog_migrate/be4b7a1459515249a4b3cb632207873b.png)

**[Step 2]：** 键盘布局 ➡️ 更新和其它软件

![](https://i-blog.csdnimg.cn/blog_migrate/0c45c87f785f8063cd39e883ca924759.png)

**[Step 3]：** 安装类型 ➡️ 你在哪

![](https://i-blog.csdnimg.cn/blog_migrate/8b54f3719adca79c0a2a15e62ad58f62.png)

**[Step 4]：** 你是谁 ➡️ 安装完成，重启

![](https://i-blog.csdnimg.cn/blog_migrate/ab0ebd1436af36122f31d7cda83c4e04.png)

**[Step 5]：** 登录 Ubuntu 系统

![](https://i-blog.csdnimg.cn/blog_migrate/702b1f7a8ac359c29ca159676aa814fa.png)

## 6. 安装 VWware Tools

> VMware Tools 作用请参考：[https://docs.vmware.com/cn/VMware-Tools/index.html](https://docs.vmware.com/cn/VMware-Tools/index.html)

**[Step 1]：** 如下图，`重新安装 VMware Tools(T)...` 是灰色的，则需要关闭虚拟机

![](https://i-blog.csdnimg.cn/blog_migrate/f9046ef74ead68b3f806c1d8a842ef13.png)

![](https://i-blog.csdnimg.cn/blog_migrate/11a8f99a1b6bb725c77ac91cc321636b.png)

**[Step 2]：** 在 `编辑虚拟机设置` 中，将 `CD/DVD (SATA)<span> </span>`的连接修改为`使用 ISO 镜像文件(M)`，然后重启虚拟机

![](https://i-blog.csdnimg.cn/blog_migrate/852661a52d83b0a8d28f4d1fb94eb74a.png)

> **作者试了下，好像也是不可以的？？？**

> 直接在线安装吧！（参考自 [解决虚拟机安装 VMware Tools 灰色无法点击问题](https://blog.csdn.net/qq_36393978/article/details/128624330)）

```shell
# 更新软件源列表
sudo apt-get update

# 更新软件
sudo apt-get upgrade

# 下载安装 open-vm-tools-desktop
sudo apt-get install open-vm-tools-desktop -y
```

![](https://i-blog.csdnimg.cn/blog_migrate/fe46d70c4c2cc9cebc6f3b8e5c76c7a9.png)

---

**VMware Workstation 17 Pro 下载：**[https://www.vmware.com/content/vmware/vmware-published-sites/us/products/workstation-pro/workstation-pro-evaluation.html.html](https://www.vmware.com/content/vmware/vmware-published-sites/us/products/workstation-pro/workstation-pro-evaluation.html.html)

**完美解决VMware安装后没有VMnet1和VMnet8的问题：**[https://blog.csdn.net/KRISNAT/article/details/124107032](https://blog.csdn.net/KRISNAT/article/details/124107032)

**Ubuntu 22.04.4 LTS 下载：**[https://ubuntu.com/download/desktop](https://ubuntu.com/download/desktop)

**清华大学开源软件镜像站 - Ubuntu 22.04.4 下载：**[https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/22.04.4/](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/22.04.4/)

**解决虚拟机安装 VMware Tools 灰色无法点击问题：**[https://blog.csdn.net/qq\_36393978/article/details/128624330](https://blog.csdn.net/qq_36393978/article/details/128624330)

**VMware Tools 作用：**[https://docs.vmware.com/cn/VMware-Tools/index.html](https://docs.vmware.com/cn/VMware-Tools/index.html)
