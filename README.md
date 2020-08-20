---
title:  "Hackintosh记录"
categories: CS/Notes
tags: Hackintosh
author: Posted by CJ.
---

>关于家里主力输出设备台式机折腾黑苹果的一些总结记录。项目中的CLOVER经过不断测试优化已99%完美，目前稳定运行ing：）

![](http://githubluffythinkio.oss-cn-beijing.aliyuncs.com/typora/黑果-双系统.png)

## 黑果配置（台式）

- CPU：Intel 8700K
- 主板：[微星Z370 TOMAHAWK](https://cn.msi.com/Motherboard/Z370-TOMAHAWK.html)
- 声卡：Realtek ALC892
- 显卡：RX580蓝宝石4G
- 无线网卡+蓝牙：博通BCM94360
- 硬盘：威刚M.2-S11Pro 256G + WD西部数据2T 
- 内存：十铨8GX2 DDR4 2666
- 显示器：Dell P2715Q 

## 硬件/功能测试 - 基本完美

- **MacOS版本支持**：Catalina10.15.X

- **硬件**

  <img src="http://githubluffythinkio.oss-cn-beijing.aliyuncs.com/typora/黑果硬件温度截图.png" style="zoom:50%;" />

  ![](http://githubluffythinkio.oss-cn-beijing.aliyuncs.com/typora/clover.png)

1. 显卡已驱动，HDMI、DP 接口正常
2. 声卡已驱动，支持显示器和主板前后置输出
3. 有线网卡+无线WIFI+蓝牙均OK
4. USB2.0，3.0，3.1，硬盘接口正常
5. CPU 原生变频睿频，性能跑分正常（BIOS 内自行超频）

- **功能测试**

1. 加载 XCPM 原生电源管理，睡眠唤醒正常（睡眠后按电源键唤醒）
2. 显卡 HVEC 硬解加速支持
3. 稳定性测试无死机重启
4. **airdrop**隔空投送正常，同一网络环境实现苹果设备间视频照片文档等文件传输，无需单独组网；**handoff**无线接力OK，可与邮件、Safari等及被支持的第三方应用使用，实现手机与电脑高效率协作；**airplay**无线投屏OK，可与投影电视作为扩展屏使用，最好用有限局域网，无线比较卡

## 安装准备工作

> 相关安装教程参考[黑果小兵](https://blog.daliansky.net/)，这里简要列一下重要步骤：

1. **系统镜像**文件下载 ：目前更新到Catalina10.15.3正式版。下载系统请校验文件 MD5 值，以免系统在下载过程中由于网络原因导致文件损坏，从而导致制作等安装启动盘作废。
2. **U 盘安装启动盘**制作，具体可参考黑果小兵
3. 安装前**BIOS设置**，推荐`UEFI模式`，支持与WIN10组双系统

   > 以下是BIOS的一些记录参考：

   ```
   0. Save & Exit →Load Optimized Defaults
   1. XHCI Hand-off :Enabled
   2. Above 4G decoding :Enabled
   3. 将操作系统类型设置为：Other OS
   4. CSM :disabled
   # 兼容性支持模块（CSM）是UEFI固件的组件，该组件通过模拟BIOS环境来提供旧版BIOS兼容性，从而允许仍使用旧版操作系统和某些不支持UEFI的选件ROM。Clover和OC引导都支持UEFI引导。禁用CSM使BIOS可以轻松发现Bootloader。
   
   5. 禁用安全启动/快速启动
   # 安全启动可防止从任何内部磁盘或USB驱动器启动未签名的BootloaderClover或OC引导不支持安全启动。必须在BIOS中禁用安全启动才能启动黑苹果。
   
   6. 将SATA设置为AHCI
   # 通过高级主机控制器接口（AHCI）模式，可以在SATA驱动器上使用高级功能，例如热插拔和本机命令队列（NCQ）。AHCI还允许硬盘以比传统IDE模式更高的速度运行。
   
   7. CFG_look disabled
   # CFG锁定可防止macOS写入BIOS中的特定区域。macOS出于电源管理和其他原因执行此操作，并且如果无法访问它，它将无法启动。
   
   8. VT-D disabled
   # VT-d特别是IOMMU规范。扩展允许您访问虚拟机下的物理硬件（例如运行Linux的系统可以在虚拟机上运行Windows。如果没有VT-d，则视频卡会被仿真，并且游戏速度会很慢。视频卡可以进入直通模式，并且可以在Windows下作为真实硬件（可以安装nvidia驱动程序）进行访问，并且视频卡的性能类似于运行本机Windows实时预览的情况。但是对于许多黑苹果用户，VT-D不会造成任何问题，但是如果您是新手，则尝试安装和配置Hackintosh禁用VT-D并安装。您可以在安装后根据需要启用VT-D。
   
   9. 禁用英特尔虚拟化技术/VT-X
   # 多个英特尔CPU随英特尔虚拟化技术一起提供。此技术以前称为Vanderpool，它使CPU可以像具有多台独立的计算机一样工作，以便使多个操作系统可以在同一台计算机上同时运行。英特尔虚拟化技术（VT）也称为VT-x扩展，它允许在虚拟机下直接访问CPU，从而使VMWare / Parallel Desktop等虚拟化软件的性能更好。您可以在需要后在安装后启用虚拟化技术。
   
   10. PCI Latency timer PCI延迟时钟。这个指pci设备独占pci总线的时间，值越大，则这个设备利用带宽越充分，但是后面排队的设备就必须等待更长的时间才能开始独占pci总线并工作；所以设置大了可以提高一个设备的速度，设置小了则提高对多个pci设备的响应效率。主板一般默认是32，Omega小组推荐设置成64。
   ```

4. 尽量下载匹配的EFI 文件，在` PE `下进行 EFI 文件的替换操作，进行系统安装与测试、

   >注意：使用本EFI，由clover引导： `config.plist` 文件中机型以及主板序列号等信息需要修改。
   
5. **Tools参考:**

   1. PE工具
   2. TransMac
   3. CloverConfigur四叶草
   4. Hackintool
   5. EasyUEFI
   6. Intel Power Gadget
   7. iStat Menus
   8. Geekbench跑分
   9. Kext Utility
   10. Macs Fan Control 2

## clover、oc引导

>关于OC引导可参考[这里](https://blog.xjn819.com/)。

这里我整理了一份clover四叶草引导的简要说明**思维导图**。仅参考。

![](https://raw.githubusercontent.com/luffythink/Hackintosh-EFI/master/Hackintosh-EFI-%20Clover%E6%96%87%E4%BB%B6%E7%BB%93%E6%9E%84.png)



## Debug更新记录

1. 因错误设置，黑果开机黑屏状态直接进入系统，进入不了BIOS？

   **解决：**PEG模式是独立显卡，集成显卡必须是允许状态，不然开机黑屏状态，进不了BIOS，只有扣纽扣电池。

   ![](http://githubluffythinkio.oss-cn-beijing.aliyuncs.com/typora/黑苹果记录debug.png)

   > 扣纽扣电池后，可能电脑的系统启动项需要重新设置，不然默认直接进入win系统。出现BIOS恢复出厂设置可能原因：系统待机一直没启动，之前BIOS设置连续按开机按钮导致。

2. CPU温度突然非常高？

   ![](http://githubluffythinkio.oss-cn-beijing.aliyuncs.com/typora/黑苹果debug.png)

   **解决：**问题排查，开始以为是夏天风扇扇热问题（还以为是pwm/dc模式问题）。但经过iStat查看之前历史温度是正常的，排除风扇问题。后发现某个进程占用CPU资源特别大，锁定了mds_stores目标。搜索了解：mds_stores是spotlight的后台进程。

   > spotlight为了用户在查询数据的时候能够快速显示查找结果，所以需要对这些文件建立索引等信息。mds_stores就是后台在建立索引等信息的进程。在建立这些信息的时候，需要对这些文件进行读取分析，并且写入索引等导致磁盘读写非大。这里可能与我们的电脑分区有关，固态硬盘+机械盘+双系统，可能存在大量文件的更新。

   >```bash
   ># 本身使用过程中查询不多，可把spotlight和mds_stores关闭
   ># 关闭spotlight的命令：
   >sudo launchctl unload -w /System/Library/LaunchAgents/com.apple.Spotlight.plist
   ># 开启：
   >sudo launchctl load -w /System/Library/LaunchAgents/com.apple.Spotlight.plist
   ># 关闭mds_stores的命令：
   >sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.metadata.mds.plist
   ># 开启：sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.metadata.mds.plist
   >```


## 黑果优化

1. 命令调试检查

   ```bash
   # 检查 XCPM 是否正常加载，返回 1为正常
   $ sysctl machdep.xcpm.mode
   # 验证 X86PlatformPlugin.kext 是否已经加载
   $ kextstat|grep -y x86plat
   # 验证 Apple Intel CPU 电源管理，返回为空表示正常
   $ kextstat|grep -y appleintelcpu
   # 验证是否加载变频，返回 1 为正常
   $ sysctl -n machdep.xcpm.vectors_loaded_count
   ```
   
2. Mac升级后配置上的小红点去掉方法

   ```bash
   # 在终端执行下面命令：
   $ defaults write com.apple.systempreferences AttentionPrefBundleIDs 0
   $ killall Dock
   ```

3. 禁止系统更新提示代码命令

   ```bash
   $ sudo softwareupdate --ignore “macOS Catalina”
   # 放弃更改，使用下面进行重置
   $ sudo softwareupdate --reset-ignored
   ```

4. 安装任何来源软件

   ```bash
   $ sudo spctl --master-disable
   # 可选：关闭sip，即System Integrity Protection系统完整保护，将一些文件目录和系统应用保护了起来
   $ csrutil disable
   # 查看关闭状态 
   csrutil status
   ```
   
5. 本Mac和Win系统的时间不同步问题及解决方法：

   ```bash
   # win系统里，请在管理员cmd运行命令如下：
   Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
   ```

6. 安装Mac os 后“其他宗卷”占用大量空间，这个问题通常是Mac 的“safe sleep”功能开启导致的，关闭方法如下：

   ```bash
   # 在终端中输入下面的命令
   $ sudo pmset -a hibernatemode 0
   # 然后定位到/private/var/vm/删除已经存在的sleepimage文件
   ```

7. 升级10.15后，开启hidpi用之前的脚本方式无法开启，新系统system/library变成了只读，脚本文件无法写入，解锁方式：

   ```bash
   $ sudo mount -uw /
   $ killall Finder
   ```

8. Kext Utility了解，由于macOS 10.15 锁住了S/L/E的修改权限，因此在修改kext前要使用终端先解锁System/Library/Extensions权限

   ```bash
   # 打开终端依次输入
   $ sudo su
   $ sudo mount -uw /
   $ killall Finder
   # 重建缓存
   sudo touch /System/Library/Extensions && sudo kextcache -u 
   ```


## 其它记录

- win7&黑苹果`双系统`（对win7镜像有要求）、Win10&黑苹果双系统
  
  >**双系统分区注意：**Mac机械硬盘用HFS+（Mac OS 扩展），固态用APFS（对固态有优化）。WIN上用NTFS（Fat32 虽然稳定但单个文件不能超4g）。频繁Mac和WIN之间用exFAT，作为一个中转站的移动硬盘。
- 双显卡交火，组双RX580，瞎折腾
- 雷电三（ThunderBolt3）接口的问题，可能与主板有关

## 参考资料

>感谢以下作者，及[黑果小兵](https://blog.daliansky.net/)和[远景论坛](http://bbs.pcbeta.com/)的提供的黑果交流平台。


1. [MAC 10.14 安装教程4-制作安装EFI文件](https://www.jianshu.com/p/2ad57fca5969)
2. [8700k+z370 aorus gaming5硬件加速的感受以及efi分享](http://bbs.pcbeta.com/viewthread-1823693-1-1.html)
3. [AptioMemoryFix-64.efi和OsxAptioFix2Drv-free2000.efi区别](http://bbs.pcbeta.com/viewthread-1804394-1-1.html)
4. [黑苹果驱动硬件传感器获取 CPU 温度详细信息](https://blog.zuiyu1818.cn/posts/Hac_CPU_sensors.html)
5. [黑果小兵：DW1820A/BCM94350ZAE/BCM94356ZEPA50DX插入的正确姿势](https://blog.daliansky.net/DW1820A_BCM94350ZAE-driver-inserts-the-correct-posture.html)
7. [successful-build-extended-guide.227001/
   KernelAndKextPatches 10.13x,10.14.x,10.15.x X99 by nmano](https://www.insanelymac.com/forum/topic/335650-kernelandkextpatches1013x1014x1015x-x99/)
7. [Hackintosh内建雷电三简单驱动教程](https://zhuanlan.zhihu.com/p/75536927)
8. [BROADCOM BCM4350 CARDS UNDER HIGH SIERRA/MOJAVE/CATALINA](https://osxlatitude.com/forums/topic/11322-broadcom-bcm4350-cards-under-high-sierramojavecatalina/)
9. [OSX活动监视器关闭spotlight 、mds_stores等进程](https://blog.csdn.net/github_38885296/article/details/80558825?utm_source=blogxgwz1)
