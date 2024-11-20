删除文件夹下大小为0的文件

```
find . -name "*" -type f -size 0c | xargs -n 1 rm -rf
```

挂载U盘报错重新挂载

```shell
sudo ntfsfix /dev/报错编号  
```

输入法失效时解决

```shell
killall fcitx 
fcitx -d
```

安装搜狗输入法所需库

```shell
sudo apt-get install qtdeclarative5-dev
sudo apt-get install libgsettings-qt1
```

ubuntu图标异常修复

```
sudo apt install --reinstall librsvg2-2 librsvg2-common
```

Ubuntu+搜狗输入法禁止简繁切换

```
sudo vim ~/.config/fcitx/conf/fcitx-chttrans.config
sudo vim ~/.config/sogoupinyin/conf/env.ini
ShortCutFanJian=1 >> ShortCutFanJian=0
```

