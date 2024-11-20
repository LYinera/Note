1. 下载tcpdump工具。

2. 运行

   ```c++
   tcpdump -i lo -s0 -w 文件路径 
   //i interfere 接口，lo localhost 本机地址，s0 正常取tcp数据因为长度问题会发生截断，使用s0参数表示dump全部数据，-w写入文件，后面跟文件路径，格式为pcap文件
   ```

3. 查看与分析

   使用wireshark查看dump出来的数据并进行分析