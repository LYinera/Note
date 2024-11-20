##  修改fidl文件

=> 添加Command

```
    enumeration Command {
		...
		SEND_UPDATE_BLE_STATUS
    }
```

=> 定义数据类型（不需要可以不进行定义）

```
struct BLEConfig {
    String path
    StatusCode statusCode
}
```

=> broadcast service主动发布广播，client接收

```
broadcast UpdateBleHandler {
    out {
        BLEConfig bleConfig
    }
}
```

=> method 提供给client调用的函数

```
method SendUpdateBleStatus {
    in {
        BLEConfig bleConfig
    }
}
```



## method 创建

1. ### 在service中重载对应函数

   ```
   void MyMqttserviceInterfaceStub::SendUpdateBleStatus(mqttserviceInterface::BLEConfig bleConfig)
   {
   	MQTTService_SendUpdateBleStatus(bleConfig);
   }
   ```

2. ### 实现函数

   1. #### 在重载函数调用的函数中将Command设置为fidl文件添加的Command

      ```
      int MQTTService_SendUpdateBleStatus (mqttserviceInterface::BLEConfig bleConfig) {
      	pthread_mutex_lock(&MQTTMutex);
      	MQTTServiceRef.queryInfoBypassInfo.nBusinessID = QUERY_INFO_BYPASS_BUSINESS_ID;
      	pthread_mutex_unlock(&MQTTMutex);
      
      	CmdRequest_t request;
      	memset(&request, 0, sizeof(CmdRequest_t));
      	request.command = mqttserviceInterface::Command::SEND_UPDATE_BLE_STATUS; // 定义的Command
      	mqttserviceInterface::BLEConfig *pBleConfig = new mqttserviceInterface::BLEConfig();
      	request.context = pBleConfig;
      	*pBleConfig = bleConfig;
      	pthread_mutex_lock(&MqttCmd_vector_mutex);
      	theMqttCmd.push_back(request);
      	pthread_mutex_unlock(&MqttCmd_vector_mutex);
      	WriteDebugInfo("[UPDATE_BLE]%s, theMqttCmd push_back[%d] done", __FUNCTION__, request.command);
      	return 0;
      }
      ```
   
   2. #### 在MQTTService中的ProcessCommand添加用于处理对应Command的case
   
      ```
      case mqttserviceInterface::Command::SEND_UPDATE_BLE_STATUS:
      {
          WriteDebugInfo("[UPDATE_BLE]mqttserviceInterface::Command::SEND_UPDATE_BLE_STATUS");
          SendUpdateBleStatus_SendMQTTMessage(context);
      }
          break;
      ```
      
   3. #### 实现case中的处理函数，拼装报文，进行发送
   
      ```
      static bool SendBLEAction_SendMQTTMessage(void *context) {
      	char szPAYLOAD[MAX_PAYLOAD_SIZE] = {0};
      	char value[MAX_VALUE_SIZE] = {0};
      	char *out = NULL;
      	cJSON *root = NULL;
      	cJSON *jsonData = NULL;
      	cJSON *configData = NULL;
      	mqttserviceInterface::BLEConfig *pBLEConfig = (mqttserviceInterface::BLEConfig *)context;
      	if (NULL == pBLEConfig) {
      		WriteDebugInfo("[BLE]%s, pBLEConfig is NULL, return false.", __FUNCTION__);
              return false;
          }
      	root = cJSON_CreateObject();
      	pthread_mutex_lock(&MQTTMutex);
      	JSON_SET_STR_FROM_INT(root, "businessID", QUERY_INFO_BYPASS_BUSINESS_ID);
      	pthread_mutex_unlock(&MQTTMutex);
      	jsonData = cJSON_CreateObject();
      	JSON_SET_STR_FROM_INT(jsonData, "commandID", QUERY_INFO_BYPASS_COMMAND_ID_BLE);
      	cJSON_AddItemToObject(jsonData, "BLEConfig", configData = cJSON_CreateObject());
      	cJSON_AddStringToObject(configData, "path", pBLEConfig->path);
      	cJSON_AddNumberToObject(configData, "statusCode", pBLEConfig->statusCode);
      
      	out = cJSON_Print(jsonData);
      	cJSON_AddStringToObject(root, "jsonData", out);
      	cJSON_Delete(jsonData);
      	free(out);
      	out = cJSON_Print(root);
      	cJSON_Delete(root);
      	strcpy(szPAYLOAD, out);
      	free(out);
      	WriteDebugInfo("[BLE]%s, New PAYLOAD Len:%d, : %s", __FUNCTION__, strlen(szPAYLOAD), szPAYLOAD);
      	return SendMQTTmsg(szPAYLOAD);
      }
      ```
   

## broadcast创建

1. ### 在service中定义CommandID

   ```
   #define QUERY_INFO_BYPASS_COMMAND_ID_BLE					(7) //蓝牙升级
   ```

2. ### 在QueryInfoBypass_ProcMaps_T gstQueryInfoBypass_ProcMaps[]中增加用于处理对应消息的函数

   ```
   {QUERY_INFO_BYPASS_COMMAND_ID_BLE, ResBLEAction_proc},
   ```

3. ### 实现对应函数

   ```
   static int ResBLEAction_proc(cJSON *jsonData) {
   	mqttserviceInterface::BLEConfig bleConfig;
   	WriteDebugInfo("LHR-->[%s] = [0x%02p]", __func__, &bleConfig);
   
   	cJSON *configData = cJSON_GetObjectItem(jsonData, "BLEConfig");
   	WriteDebugInfo("LHR-->[%s] = [BLEConfig]", __func__);
   
   	if (NULL == configData) {
   		bleConfig.statusCode = mqttserviceInterface::StatusCode::StatusCode_FAILED;
   		MQTTService_SendBLEAction(bleConfig);
   		WriteDebugInfo("%s, BLEConfig not found.", __FUNCTION__);
   		return 0;
   	} else {
   		JSON_GET_INT(configData, "statusCode", bleConfig.statusCode, 0)
   	}
   
   	SendConnStateEvent(event_Id_t::UpdateBleActionEvent, &bleConfig, sizeof(mqttserviceInterface::BLEConfig));
   	WriteDebugInfo("[%s] = [SendConnStateEvent]", __func__);
   }
   ```

4. ### 在event_Id_t中增加对应的event

   ```
   UpdateBleActionEvent,
   ```

5. ### 在SendConnStateEvent中增加处理case，向client发送广播

   ```
   case event_Id_t::UpdateBleActionEvent:
   {
       mqttserviceInterface::BLEConfig bleConfig = *(mqttserviceInterface::BLEConfig*)event;
       WriteDebugInfo("SendConnStateEvent(%d) case event_Id_t::UpdateBleActionEvent", eventId);
       themqttserviceStub->fireAVASActionHandlerEvent(bleConfig);
   }
       break;
   ```

   