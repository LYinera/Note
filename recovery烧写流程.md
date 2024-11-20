# recovery烧写流程

## 1. recovery编译

1. 进入工作目录

2. 导入环境配置

```shell
export LC_ALL=C
source build/envsetup.sh
lunch 41
```

3. 运行编译命令`make recoveryimage`

## 2. 车机进入bootloader模式

1. 连接串口
2. 登录
3. 串口帐号密码 root 
4. Pateo_ares_link_QOS8.0
5. 输入命令`reboot bootloader`

## 3. fastboot连接设备并烧写

1. linux端在车机进入bootloader模式后运行`fastboot devices`查看设备是否连接
2. 运行`sudo fastboot flash recovery out/target/product/msm8953_32/recovery.img`完成recovery烧写
3. 运行`fastboot reboot recovery`重新启动