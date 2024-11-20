**1.MPU是如何避免项目间交叉升级的(例如使用DE5LINK升级包给G35LINK升级)。**

通过install.cpp中的really_install_package()调用verify_package读取证书，使用公钥进行解密，当特定位置的值为空时该包为未签名包，报错，当该位置为非特定值时该包为非本项目包，报错。由此避免项目间交叉升级。

同时，mpu升级updater-script脚本中也有对版本号的相关检验

**2.MCU是如何避免项目间交叉升级的。(提示：需要考虑升级包脚本)**

在mcu升级updater-script脚本中对mcu升级包版本号进行校验来避免项目件轿交叉升级

**3.当前U盘升级MPU会格式化哪些分区；(提示：需要考虑升级包脚本)**

格式化private分区

format("ext4", "EMMC", "/dev/block/bootdevice/by-name/private", "0", "/private");

**4.恢复出厂设置会格式化哪些分区；(提示：/tmp/recovery.log)**

会擦除cache与data分区

**5.如何在recovery主界面菜单追加新入口**

在devices的MENU_ITEMS数组中添加新的选项，同时添加对应的actions以及对应的key

**6.如何通过系统minirecovery指令进入recovery，原理如何；(提示：查看系统fix.sh脚本)**

通过minirecovery命令写入升级包路径以及升级包种类，输入reboot后即可进入升级界面，升级失败则进入recovery模式

将启动信息写入BCB,bootloader启动时读取该分区判断启动模式

**7.通过指令reboot recovery进入recovery的原理**

将启动信息写入BCB,bootloader启动时读取该分区判断启动模式

**8.Recovery系统的三个部分和两个通信接口的示意图**

![a3719ae28eba28eb15f8f5209a93c468](/home/lhr/Downloads/a3719ae28eba28eb15f8f5209a93c468.png)

**9.recovery.cpp的main方法执行的流程图**

![0e20b66d4287ef67dc98c46d11167a4a](/home/lhr/Downloads/0e20b66d4287ef67dc98c46d11167a4a.png)