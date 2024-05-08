## 编译方法

`./mk.sh nanopi_r5c_defconfig`
`./mk.sh -j8`

- 产物： `arch/arm64/boot/Image`

## 设备树

- 开发板设备树生成 dtb，比如 `rk3568-nanopi5-rev02.dtb`

## 配置方法

- `./mk.sh menuconfig`

- `./mk.sh savedefconfig`

- 可以保存为一个开发板特有的 defconfig，比如复制 defconfig 到 `cp defconfig arch/arm64/configs/nanopi_r5c_defconfig`

## 启动引导参数

```shell
bootargs=console=ttyS2,1500000 earlycon=uart8250,mmio32,0xfe660000 root=/dev/mmcblk2p3 rootfstype=ext4 rootdelay=3 rw rootwait
bootcmd=ext4load mmc 0:2 0x280000 Image; ext4load mmc 0:2 0x8300000 rk3568-nanopi5-rev02.dtb; booti 0x280000 - 0x8300000

setenv bootcmd 'ext4load mmc 0:2 0x280000 Image; ext4load mmc 0:2 0x8300000 rk3568-nanopi5-rev02.dtb; booti 0x280000 - 0x8300000'
setenv bootargs 'console=ttyS2,1500000 earlycon=uart8250,mmio32,0xfe660000 root=/dev/mmcblk2p3 rootfstype=ext4 rootdelay=3 rw rootwait'
saveenv
reset
```

- 备注：如果 emmc 无法正常挂载，建议启动参数 bootargs 中增加启动延时，比如 `rootdelay=3`

## 生成 modules

- `make CROSS_COMPILE=aarch64-linux-gnu- ARCH=arm64 INSTALL_MOD_PATH="$PWD/_modules" modules`

- `make CROSS_COMPILE=aarch64-linux-gnu- ARCH=arm64 INSTALL_MOD_PATH="$PWD/_modules" modules_install`

复制 _modules 下 lib 目录下所有到 rootfs/lib 目录下

## RTL8125 网卡固件

- `rootfs/lib` 下创建 firmware 目录

- 复制 [https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git](https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git) 中的 rtl_nic d到 根文件系统 `rootfs/lib/firmware/` 目录下

## 生成 rootfs.img

- 使用 `make_rootfs_img.sh` 脚本

```shell
#!/bin/bash
make_ext4fs -s -l 8192M rootfs.img rootfs_new/
```

