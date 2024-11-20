1. Linux man page number

```shell
[dmtsai@study ~]$ man date
DATE（1）                          User Commands                         DATE（1）
```

| 代号 | 代表内容                                                     |
| ---- | ------------------------------------------------------------ |
| 1    | 使用者在shell环境中可以操作的指令或可可执行文件              |
| 2    | 系统核心可调用的函数与工具等                                 |
| 3    | 一些常用的函数（function）与函数库（library），大部分为C的函数库（libc） |
| 4    | 设备文件的说明，通常在/dev下的文件                           |
| 5    | 配置文件或者是某些文件的格式                                 |
| 6    | 游戏（games）                                                |
| 7    | 惯例与协定等，例如Linux文件系统、网络协定、ASCII code等等的说明 |
| 8    | 系统管理员可用的管理指令                                     |
| 9    | 跟kernel有关的文件                                           |

2. Linux关机指令以及使用方式，实际都是通过systemctl指令进行系统操作

- shutdown 

  ```shell
  -k     ： 不要真的关机，只是发送警告讯息出去！
  -r     ： 在将系统的服务停掉之后就重新开机（常用）
  -h     ： 将系统的服务停掉后，立即关机。 （常用）
  -c     ： 取消已经在进行的 shutdown 指令内容。
  
  [root@lyinera ~]# shutdown -h now
  立刻关机，其中 now 相当于时间为 0 的状态
  [root@lyinera ~]# shutdown -h 20:25
  系统在今天的 20:25 分会关机，若在21:25才下达此指令，则隔天才关机
  [root@lyinera ~]# shutdown -h +10
  系统再过十分钟后自动关机
  [root@lyinera ~]# shutdown -r now
  系统立刻重新开机
  [root@lyinera ~]# shutdown -r +30 'The system will reboot' 
  再过三十分钟系统会重新开机，并显示后面的讯息给所有在线上的使用者
  [root@lyinera ~]# shutdown -k now 'This system will reboot' 
  仅发出警告信件的参数！系统并不会关机啦！吓唬人！
  ```

- sync 将内存信息写入磁盘（防止断电丢失数据）
- halt  系统停止～屏幕可能会保留系统已经停止的讯息！
- poweroff  系统关机，所以没有提供额外的电力，屏幕空白！