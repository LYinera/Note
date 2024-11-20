repo => 按照manifest配置的仓库下载代码 => 封装的git

repo下载报错 => 没有权限/空间不足/其他原因

当由于没有权限导致的下载报错

=> 需要使用的话去申请权限

=> 不需要使用的将代码仓库从manifest进行删除（使repo不再下载对应仓库）

1. 对应的manifest文件位于下载执行repo sync命令的文件夹下 ./.repo/manifests/default.xml

2. 查看报错仓库，通过grep指令进行查找仓库名位于default.xml的位置

   ```
   cd ./.repo/manifests
   grep -Hrnsi vendor //以vendor为例
   ```

   此时会输出包含vendor的文件名，行号以及对应语句，示例如下

   ```
   default.xml:838:  <project name="vendor" path="vendor/pateo" remote="qingos_240"  revision="qing-1.7_6125_dev" />
   ```

3. 按照提示打开default.xml文件对应位置将对应语句删除，重新返回之前目录执行repo sync

   ```
   vim default.xml +838
   dd
   :wq
   cd -
   repo sync
   ```

   

​	

