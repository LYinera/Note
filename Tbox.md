#### 1. TBox是什么时候进行与云端的链接的？

**猜测：**在开机时以及需要时进行与云端的链接

**证实：**



2. VIN冲突的检查时机

   电源状态从OFF 变更未 非OFF；或者从非OFF变更为OFF。启动检测线程VINConflictLoop()

   Product_VIN获取方式：SysConfigEntity::getVhlVinFromCis（）调用CIS接口从MCU获取

   ​	1.上电从mcu读取vin，获取不到则从数据库中获取，数据库获取不到则用pdsn+0;
   ​	2.上电从mcu获取到后写入数据库

   TSP_VIN获取：TSP下发给Tbox，远程配置协议0x7 0x11; 

   ​	调用decodeRemoteConfig()，解析后存储到数据库中？？

   | 0x7E | 0x10 |      |      |                  |      |       |      |                                                              |                                     |
   | ---- | ---- | ---- | ---- | ---------------- | ---- | ----- | ---- | ------------------------------------------------------------ | ----------------------------------- |
   |      | 0x7D | 0x01 |      |                  | N    |       |      |                                                              |                                     |
   |      |      | 0x7C | 0x02 | ATT_LANGUAGE_USE | 1    |       |      | 0=法语 1=英语 2=德语 3=西班牙语 4=意大利语 5=葡萄牙语 6=荷兰语 7=希腊语 8=巴西葡萄牙语 9=繁体中文 10=简体中文 11=土耳其语 12=日语 13=俄语 | 固定值10=简体中文，后台不会更新下发 |
   |      |      | 0x7C | 0x06 | ATT_CON_URL      | 128  | ASCII |      | 连接东风TSP云平台的URL地址                                   | 固定值，后台不会更新下发            |
   |      |      | 0x7C | 0x07 | ATT_OTA_URL      | 128  | ASCII |      | Tbox OTA升级的URL                                            | 固定值，后台不会更新下发            |
   |      |      | 0x7C | 0x0E | ATT_VHL_VIN      | 17   | ASCII |      |                                                              | VIN冲突时下发                       |

   VIN比较：

   分别从数据库中获取Product_VIN、TSP_VIN(getTspVin())

3. 诊断功能

   WriteInfoToMcu()，发送TSP_VIN、IMEI、ICCID、URL给MCU，用于诊断

4. 远程配置的处理逻辑

   -- 云端下发7-11配置请求（携带配置参数和唯一流水号）

   -- Tbox端解析配置消息，并处理。 处理函数：TspClient::processVHLConfigCmd()？？？

   ​    NGTPEnDeCoder::decode()????-》decodeRemoteConfig

   -- Tbox返回执行结果7-17

5. 日志关键字

   查看CAN信号：grep -Hrns "parseVehicleCanBuf02n" /tbox/pateo/logs/logservice

   查看CIS日志：cat /private/log/cis_data/cis_data.log |grep "16 6D"

6. 

