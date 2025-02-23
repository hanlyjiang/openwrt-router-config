# LinkSys WRT32X 刷机及使用

## 固件

### 在线下载：

- 官方固件：[WRT32X Downloads, Documents, and User Guide - Linksys Support](https://support.linksys.com/kb/article/1030-en/)
- DDWRT: [DD-WRT » Other Downloads](https://dd-wrt.com/support/other-downloads/?path=betas%2F2025%2F02-20-2025-r59765%2Flinksys-wrt32x%2F)
- OpenWRT：[OpenWrt Firmware Selector](https://firmware-selector.openwrt.org/?version=24.10.0&target=mvebu%2Fcortexa9&id=linksys_wrt32x)

### 仓库备份：

- 官方固件：
  - 官方 kernel 刷机包： [flat-FW_WRT32X_1.0.180404.58.img](firmware\stock\flat-FW_WRT32X_1.0.180404.58.img) 
  - 官方 web 刷机包： [FW_WRT32X_1.0.180404.58.img](firmware\stock\FW_WRT32X_1.0.180404.58.img) 
- DDWRT: [ddwrt](firmware\ddwrt) 
- OpenWRT:  [openwrt](firmware\openwrt) 



##  刷机

WRT32X 有两个分区，在当前分区web刷机，更新的是另外一个分区的系统，**如果想保留原厂系统，刷机时，需要切换到原厂系统分区**来刷写DDWRT或者openwrt。

### 切换分区方法：

#### 1. 重启三次法：

1） 开机 - 电源灯亮后关机

2） 开机 - 电源灯亮后关机

3） 开机 - 电源灯亮后关机

4） 开机

#### 2. 命令切换：

通过SSH连接到路由器，执行如下命令：

**DDWRT 命令：**

```BASH
## DDWRT
# 查看当前分区
ubootenv get boot_part
# 设置下次启动分区 1
ubootenv set boot_part 1
# 设置下次启动分区 2
ubootenv set boot_part 2
```

**openWRT 命令：**

```bash
## OpenWRT
# 查看当前分区
fw_printenv boot_part
# 设置下次启动分区 1
fw_setenv boot_part 1
# 设置下次启动分区 2
fw_setenv boot_part 2
```

然后通过 reboot 命令重启

### 刷机步骤

#### DDWRT

1. 首次：直接下载对应的 factory 刷机包如： [FW_WRT32X_1.0.666_DDWRT.img](firmware\ddwrt\2025-02-20-2025-r59765\FW_WRT32X_1.0.666_DDWRT.img) ，在web界面升级即可
2. 后续：使用 webflash 包： ddwrt-linksys-wrt32x-webflash.bin

#### openWRT

1. 首次：使用 factory 刷机包，如： [openwrt-24.10.0-mvebu-cortexa9-linksys_wrt32x-squashfs-factory.img](firmware\openwrt\openwrt-24.10.0-mvebu-cortexa9-linksys_wrt32x-squashfs-factory.img) 在web界面升级即可
2. 后续：在使用 sysupdate 包更新

 

## 恢复原厂系统

如果两个分区都被刷成了非原厂系统，可通过如下方式恢复：

### 1. 下载系统镜像

官方 kernel 刷机包： [flat-FW_WRT32X_1.0.180404.58.img](firmware\stock\flat-FW_WRT32X_1.0.180404.58.img) 

### 2. SCP 到路由器

```BASH
scp flat-FW_WRT32X_1.0.180404.58.img root@192.168.100.1:/tmp/flat.img
```

### 3. 刷写

查看当前所处分区: 

```BASH
# ddwrt 执行
ubootenv get boot_part

# openwrt 执行
fw_printenv boot_part
## 输出： boot_part=1
```

根据输出结果，如果当前在分区2，则执行如下命令刷写分区1：

```BASH
erase nvram;

# DDWRT 执行：
mtd -e linux -r write /tmp/flat.img linux

# OPENWRT 执行：
mtd -r -e kernel2 write /tmp/flat.img kernel2
```

如果当前在分区1，则执行如下命令刷写分区2：

```BASH
erase nvram;

# DDWRT 执行：
mtd -e linux -r write /tmp/flat.img linux

# OPENWRT 执行：
mtd -r -e kernel1 write /tmp/flat.img kernel1
```



## 参考资料：

- [DD-WRT :: View topic - [SOLVED\] WRT32X back to stock Firmware](https://forum.dd-wrt.com/phpBB2/viewtopic.php?t=316094)



