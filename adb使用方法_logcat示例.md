## adb 使用方法

#### 1.将车机连接串口以及adb线

#### 2.打开串口进入车机adb模式

此处每种车机可能有所不同，风神系列统一为在串口输入指令

```shell
otg_control.sh 1
```

输入上述指令后继续输入

```shell
adbd .
```

此时车机进入adb状态，退出此状态使用

```shell
otg_control.sh 0
```

**完成此操作后不要关闭串口窗口**

#### 3.打开cmd并连接车机

###### 3.1 确认电脑识别到设备，若出现设备代码则识别成功

```shell
adb devices
```

###### 3.2 adb进入root状态

此时将会把车机退出adb状态，需要再次在串口中使用adbd .命令进入adb状态

```shell
adb root
```

###### 3.3 重新挂载adb

```shell
adb remount
```

#### 4. 使用logcat命令取出log

清除旧日志命令(使用后将清除车机内旧的日志从当前日志开始输出)

```
adb logcat -c
```

-v time参数为按照时间打印log

路径为需要保存日志的文件路径

例：D:\log.log

```
adb logcat -v time > 路径
```

