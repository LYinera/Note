1. **问题现象：**

   ```
   linux(ubuntu)开机卡在黑屏界面并提示/dev/sd.../clean xxxxxx/xxxxxx无法开机无法操作系统
   ```

2. **解决思路：**

   此时问题产生原因有两种可能：

   1. 内存/存储不足，需要增加/清理内存/存储容量
   2. 缺少显示驱动导致无法启动，需要安装驱动。本篇为此种情况

3. **解决方法：**

   1. **准备工作：**

      1. 将适用于自己显卡型号的驱动从`NVIDIA`官网进行下载并拷贝进U盘

      2. 如果为第一次启动需要准备对应自己Linux版本的`/etc/apt/sources.list`文件（也保存在U盘当中）以切换Linux下载源避免网络原因无法下载所需要软件

   2. **正式开始：**

      **首先进入recovery模式，方法如下：**

      1. 开机按`Esc`键盘进入多机启动菜单
      2. 选择 `Advanced options for Ubuntu`-->回车
      3. 选择一个 (recovery mode)

      **此时进入recovery菜单页面，内容含义如下：**

      ```
      resume: 退出 recovery 模式，然后正常启动;
      clean: 尝试清理垃圾文件，腾出更多的空间;
      dpkg: 修复损坏的包;
      fsck: 检查所有文件系统;
      grub: 更新 grub 的启动载入器;
      network: 启动网络;
      root: 进入命令行模式;
      system-summary: 系统概览，查看电脑的基本信息;
      ```

      **编译`NVIDIA`驱动并安装**

      1. 选择network连接网络

      2. 选择root进入命令行模式

      3. 使用`fdisk -l`查看插入的U盘位置，此处为`/dev/sdb1`

      4. 将U盘挂载进入Linux，命令为`mount /dev/sdb1 /mnt`，此时U盘内部文件在`/mnt`路径下已经能够查看了

      5. 导入`sources.list`文件并更新apt工具下载源，命令为`cp /mnt/sources.list /etc/apt/`，然后运行`apt-get update`以及`apt-get upgrade`

      6. 下载`gcc、g++、libc6-dev、libc-dev、make`，下载命令为`apt-get install 应用名`

      7. 禁用`Nouveau`，命令为`vim /etc/modprobe.d/blacklist.conf`在文件结尾处增加

         ```
         blacklist nouveau
         options nouveau modeset=0
         ```

      8. 更新`initramfs`，命令为` update-initramfs -u` 

      9. 重启系统`reboot`并重复执行第1234步

      10. 运行下载的驱动编译文件`/mnt/NVIDIA-Linux-x86_64-535.104.05.run`

      11. 成功安装后重启系统选择`ubuntu`即可正常进入系统

   