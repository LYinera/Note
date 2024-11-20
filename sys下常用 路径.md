## sys路径下

```c
c+s+K
# include "stdio.h"
int main(void) {
    
}
```

1、访问cis下的设备节点

```
cd class/misc/cis_mcu_mpu/   hw_version
cat hw_version 

/sys/devices 
/sys/dev/char # ls
/sys/dev/char # cat 116
cat 116\:10/dev  

/sys/dev/char # cat 116\:10/uevent 
MAJOR=116
MINOR=10
DEVNAME=snd/pcmC0D3p
```

2、通过tools工具找到对应的设备节点

```
0x09是cyttsp_key按键，0x24是cyttsp6_i2c_adapter触摸
0x0c是941芯片，0x2c是948芯片
```

```
msm8953_32:/sys/bus/i2c/devices/3-0024 # cat name                              
cyttsp6_i2c_adapter
msm8953_32:/sys/bus/i2c/devices/3-0024 # ls -al                                
total 0
......
msm8953_32:/sys/bus/i2c/devices # ls
2-0039 2-0061 3-0009 3-000c 3-0018 3-0024 3-005d i2c-2 i2c-3 i2c-6 
msm8953_32:/sys/bus/i2c/devices # cd 3
3-0009/  3-000c/  3-0018/  3-0024/  3-005d/
msm8953_32:/sys/bus/i2c/devices # cd 3-0009                                    
msm8953_32:/sys/bus/i2c/devices/3-0009 # ls 
4014_log     4024_version modalias power     uevent 
4014_version driver       name     subsystem 
msm8953_32:/sys/bus/i2c/devices/3-0009 # cat name                              
cyttsp_key
msm8953_32:/sys/bus/i2c/devices/3-0009 # i2cdetect  -y -r 3                                            
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- 04 05 06 07 -- UU -- -- UU -- -- -- 
10: -- -- -- -- -- -- -- -- 18 -- -- -- -- -- -- -- 
20: -- -- -- -- UU -- -- -- -- -- -- -- 2c -- -- -- 
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
70: -- -- -- -- -- -- -- --      

```

3.make -c  menuconfig 可视化

```
make -C kernel/msm-3.18/ menuconfig ARCH=arm CROSS_COMPILE=`pwd`/prebuilts/gcc/linux-x86/arm/arm-linux-androideabi-4.9/bin/arm-linux-androideabi- LOADADDR=0x10008000 KCFLAGS=-mno-android
```

4.打印I2C总线上有哪些设备节点；

```
msm8953_32:/sys/bus/i2c/devices # ls                                            
2-0039 2-0061 3-0009 3-000c 3-0018 3-0024 3-005d i2c-2 i2c-3 i2c-6              
msm8953_32:/sys/bus/i2c/devices #                                               
msm8953_32:/sys/bus/i2c/devices # cd i2c-3/                                     
msm8953_32:/sys/bus/i2c/devices/i2c-3 # ls                                      
3-0009 3-0018 3-005d        device  name       power     uevent                 
3-000c 3-0024 delete_device i2c-dev new_device subsystem                        
msm8953_32:/sys/bus/i2c/devices/i2c-3 # cat name                                
MSM-I2C-v2-adapter                                                              
msm8953_32:/sys/bus/i2c/devices/i2c-3 # cat uevent                              
OF_NAME=i2c                                                                     
OF_FULLNAME=/soc/i2c@78b7000                                                    
OF_COMPATIBLE_0=qcom,i2c-msm-v2                                                 
OF_COMPATIBLE_N=1                                                               
OF_ALIAS_0=i2c3                                                                 
msm8953_32:/sys/bus/i2c/devices/i2c-3 # cd 3-                                   
3-0009/  3-000c/  3-0018/  3-0024/  3-005d/                                     
msm8953_32:/sys/bus/i2c/devices/i2c-3 # cd 3-0009                               
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-0009 # ls                               
4014_log     4024_version modalias power     uevent                             
4014_version driver       name     subsystem                                    
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-0009 # cat name                         
cyttsp_key                                                                      
at uevent                                                                     < 
DRIVER=cyttsp_key                                                               
OF_NAME=cyttsp_key                                                              
OF_FULLNAME=/soc/i2c@78b7000/cyttsp_key@09                                      
OF_COMPATIBLE_0=cy,cyttsp_key                                                   
OF_COMPATIBLE_N=1                                                               
MODALIAS=i2c:cyttsp_key                                                         
at subsystem/                                                                 < 
cat: subsystem/: Is a directory                                                 
1|msm8953_32:/sys/bus/i2c/devices/i2c-3/3-0009 # cd ../                         
msm8953_32:/sys/bus/i2c/devices/i2c-3 # cd 3-                                   
3-0009/  3-000c/  3-0018/  3-0024/  3-005d/                                     
msm8953_32:/sys/bus/i2c/devices/i2c-3 # cd 3-000c                               
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-000c # cat name                         
ds90ub941                                                                       
at uevent                                                                     < 
DRIVER=ds90ux94x                                                                
OF_NAME=dsi-to-fpdlink-ti941                                                    
OF_FULLNAME=/soc/i2c@78b7000/dsi-to-fpdlink-ti941@0c                            
OF_COMPATIBLE_0=ti,ds90ub941                                                    
OF_COMPATIBLE_N=1                                                               
MODALIAS=i2c:ds90ub941                                                          
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-000c #                                  
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-000c # cd ../3-                         
3-0009/  3-000c/  3-0018/  3-0024/  3-005d/                                     
d ../3-0018                                                                   < 
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-0018 # cat name                         
lcd_bl                                                                          
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-0018 # ls                               
brightness key_bl         modalias power     test   version                     
enable     max_brightness name     subsystem uevent                             
at uevent                                                                     < 
MODALIAS=i2c:lcd_bl                                                             
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-0018 # cat version                      
7                                                                               
at max_brightness                                                             < 
254                                                                             
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-0018 # cd ../3-                         
3-0009/  3-000c/  3-0018/  3-0024/  3-005d/                                     
d ../3-0024                                                                   < 
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-0024 # cat name                         
cyttsp6_i2c_adapter                                                             
at uevent                                                                     < 
DRIVER=cyttsp6_i2c_adapter                                                      
OF_NAME=cyttsp6_i2c_adapter                                                     
OF_FULLNAME=/soc/i2c@78b7000/cyttsp6_i2c_adapter@24                             
OF_COMPATIBLE_0=cy,cyttsp6_i2c_adapter                                          
OF_COMPATIBLE_N=1                                                               
MODALIAS=i2c:cyttsp6_i2c_adapter                                                
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-0024 # cd ../3-                         
3-0009/  3-000c/  3-0018/  3-0024/  3-005d/                                     
d ../3-005d                                                                   < 
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-005d # cat name                         
ga657x                                                                          
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-005d # cat uevent                       
OF_NAME=ga657x                                                                  
OF_FULLNAME=/soc/i2c@78b7000/ga657x@5d                                          
OF_COMPATIBLE_0=goodix,ga657x                                                   
OF_COMPATIBLE_N=1                                                               
MODALIAS=i2c:ga657x                                                             
msm8953_32:/sys/bus/i2c/devices/i2c-3/3-005d # 

```

5. 使用i2C获取941芯片的状态（连接屏幕、不连接屏幕），对比差异。

   ```
   connect:
   msm8953_32:/sys # i2cdump -f -y 3 0x0c                                          
   No size specified (using byte-data access)                                      
        0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f    0123456789abcdef         
   00: 18 00 00 92 00 00 59 34 34 01 1e 00 67 35 05 00    ?..?..Y44??.g5?.         
   10: 00 00 00 8b 00 00 fe 1e 7f 7f 01 00 20 00 01 00    ...?..?????. .?.         
   20: 0b 00 25 00 00 00 00 00 01 20 20 a0 00 00 a5 5a    ?.%.....?  ?..?Z         
   30: 00 09 00 05 0c 00 00 00 00 00 00 00 00 00 81 02    .?.??.........??         
   40: 11 86 0a 00 00 00 00 00 00 00 00 00 00 00 00 8c    ???............?         
   50: 16 00 00 00 02 00 00 02 00 00 d9 00 07 06 44 63    ?...?..?..?.??Dc         
   60: 22 02 00 00 10 00 00 00 00 00 00 00 00 00 20 00    "?..?......... .         
   70: 48 ba 00 00 00 00 00 48 ba 00 00 00 00 00 80 00    H?.....H?.....?.         
   80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   90: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   b0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   c0: 00 00 82 00 68 08 00 00 40 00 00 00 00 02 ff 00    ..?.h?..@....?..         
   d0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   e0: 00 00 82 00 68 08 00 00 00 00 00 00 00 02 00 00    ..?.h?.......?..         
   f0: 5f 55 42 39 34 31 00 00 00 00 00 00 00 00 00 00    _UB941..........         
   msm8953_32:/sys #                                                       
   
   
   not connect:
   msm8953_32:/sys # i2cdump -f -y 3 0x0c                                          
   No size specified (using byte-data access)                                      
        0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f    0123456789abcdef         
   00: 18 00 00 92 00 00 59 34 34 01 00 00 24 35 05 00    ?..?..Y44?..$5?.         
   10: 00 00 00 8b 00 00 fe 1e 7f 7f 01 00 20 00 01 00    ...?..?????. .?.         
   20: 00 00 25 00 00 00 00 00 01 20 20 a0 00 00 a5 5a    ..%.....?  ?..?Z         
   30: 00 09 00 05 0c 00 00 00 00 00 00 00 00 00 81 02    .?.??.........??         
   40: 11 86 0a 00 00 00 00 00 00 00 00 00 00 00 00 8c    ???............?         
   50: 16 00 00 00 02 00 00 02 00 00 09 00 07 06 44 63    ?...?..?..?.??Dc         
   60: 22 02 00 00 10 00 00 00 00 00 00 00 00 00 20 00    "?..?......... .         
   70: 48 ba 00 00 00 00 00 48 ba 00 00 00 00 00 b8 00    H?.....H?.....?.         
   80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   90: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   b0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   c0: 00 00 82 00 40 00 00 04 40 00 00 00 00 02 ff 00    ..?.@..?@....?..         
   d0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   e0: 00 00 82 00 40 08 00 00 00 00 00 00 00 02 00 00    ..?.@?.......?..         
   f0: 5f 55 42 39 34 31 00 00 00 00 00 00 00 00 00 00    _UB941.......... 
   
   
   connect:
   msm8953_32:/sys #                                                               
   msm8953_32:/sys # i2cdump -y -f 3 0x0c                                          
   No size specified (using byte-data access)                                      
        0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f    0123456789abcdef         
   00: 18 00 00 92 00 00 59 34 34 01 17 00 67 35 05 00    ?..?..Y44??.g5?.         
   10: 00 00 00 8b 00 00 fe 1e 7f 7f 01 00 20 00 01 00    ...?..?????. .?.         
   20: 0b 00 25 00 00 00 00 00 01 20 20 a0 00 00 a5 5a    ?.%.....?  ?..?Z         
   30: 00 09 00 05 0c 00 00 00 00 00 00 00 00 00 81 02    .?.??.........??         
   40: 11 86 0a 00 00 00 00 00 00 00 00 00 00 00 00 8c    ???............?         
   50: 16 00 00 00 02 00 00 02 00 00 d9 00 07 06 44 63    ?...?..?..?.??Dc         
   60: 22 02 00 00 10 00 00 00 00 00 00 00 00 00 20 00    "?..?......... .         
   70: 48 ba 00 00 00 00 00 48 ba 00 00 00 00 00 80 00    H?.....H?.....?.         
   80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   90: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   b0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   c0: 00 00 82 00 68 08 00 00 40 00 00 00 00 02 ff 00    ..?.h?..@....?..         
   d0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   e0: 00 00 82 00 68 08 00 00 00 00 00 00 00 02 00 00    ..?.h?.......?..         
   f0: 5f 55 42 39 34 31 00 00 00 00 00 00 00 00 00 00    _UB941..........     
   
   not connect:    
   msm8953_32:/sys # i2cdump -y -f 3 0x0c                                          
   No size specified (using byte-data access)                                      
        0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f    0123456789abcdef         
   00: 18 00 00 92 00 00 59 34 34 01 00 00 24 35 05 00    ?..?..Y44?..$5?.         
   10: 00 00 00 8b 00 00 fe 1e 7f 7f 01 00 20 00 01 00    ...?..?????. .?.         
   20: 00 00 25 00 00 00 00 00 01 20 20 a0 00 00 a5 5a    ..%.....?  ?..?Z         
   30: 00 09 00 05 0c 00 00 00 00 00 00 00 00 00 81 02    .?.??.........??         
   40: 11 86 0a 00 00 00 00 00 00 00 00 00 00 00 00 8c    ???............?         
   50: 16 00 00 00 02 00 00 02 00 00 09 00 07 06 44 63    ?...?..?..?.??Dc         
   60: 22 02 00 00 10 00 00 00 00 00 00 00 00 00 20 00    "?..?......... .         
   70: 48 ba 00 00 00 00 00 48 ba 00 00 00 00 00 bd 00    H?.....H?.....?.         
   80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   90: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   b0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   c0: 00 00 82 00 40 00 00 04 40 00 00 00 00 02 ff 00    ..?.@..?@....?..         
   d0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   e0: 00 00 82 00 40 08 00 00 00 00 00 00 00 02 00 00    ..?.@?.......?..         
   f0: 5f 55 42 39 34 31 00 00 00 00 00 00 00 00 00 00    _UB941..........         
   msm8953_32:/sys # 
   
   not conn:
   msm8953_32:/sys # i2cdump -y -f 3 0x0c                                          
   No size specified (using byte-data access)                                      
        0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f    0123456789abcdef         
   00: 18 00 00 92 00 00 59 34 34 01 16 00 67 35 05 00    ?..?..Y44??.g5?.         
   10: 00 00 00 8b 00 00 fe 1e 7f 7f 01 00 20 00 01 00    ...?..?????. .?.         
   20: 0b 00 25 00 00 00 00 00 01 20 20 a0 00 00 a5 5a    ?.%.....?  ?..?Z         
   30: 00 09 00 05 0c 00 00 00 00 00 00 00 00 00 81 02    .?.??.........??         
   40: 11 86 0a 00 00 00 00 00 00 00 00 00 00 00 00 8c    ???............?         
   50: 16 00 00 00 02 00 00 02 00 00 d9 00 07 06 44 63    ?...?..?..?.??Dc         
   60: 22 02 00 00 10 00 00 00 00 00 00 00 00 00 20 00    "?..?......... .         
   70: 48 ba 00 00 00 00 00 48 ba 00 00 00 00 00 80 00    H?.....H?.....?.         
   80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   90: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   b0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   c0: 00 00 82 00 78 00 00 44 40 00 00 00 00 02 ff 00    ..?.x..D@....?..         
   d0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................         
   e0: 00 00 82 00 68 08 00 00 00 00 00 00 00 02 00 00    ..?.h?.......?..         
   f0: 5f 55 42 39 34 31 00 00 00 00 00 00 00 00 00 00    _UB941..........   
   
   ```

   6.
