## 查询login_config

```sh
_time:7d _msg:"dump login config" topic:prod* _stream:{service="config-center-v2"} level:* user_id:137692642 |fields learn_live_tab,live_voice_tab,_time,user_id |sort by (_time) desc
```

fields

```
sort：sort by (字段) desc

_time:7d _msg:"dump login config" topic:prod* _stream:{service="config-center-v2"} level:* user_id:138282226 |fields learn_live_tab,live_voice_tab,_time,user_id |sort by (_time) desc

_time:3d _msg:"dump login config" topic:prod* _stream:{service="config-center-v2"} level:* live_voice_tab:"2"|fields learn_live_tab,live_voice_tab,_time,user_id |sort by (_time) desc
```


参考：[https://docs.victoriametrics.com/metricsql/](https://docs.victoriametrics.com/metricsql/)

## 查询工单错误的日志:

```sh 
_time:[2024-07-12T14:40:40+08:00, 2024-07-19T14:40:40+08:00] _msg:"SendApprovalBotMsg" * topic:prod* _stream:{service="cms-api"} level:error |sort by (_time) desc |fields err,caller,_time,workOrderId,trace_id,  
stacktrace

|fields err,caller,_time,workOrderId,trace_id,
```


使用正则表达式

```sh
_time:2d _msg:"AggregatePageQuery userTaskCompletion"   userTaskCompletion.host_datas:~".*142193141.*" topic:prod* _stream:{service="config_center"} level:* 
```


使用 traefik

```sh
_time:1d _msg:*  request_X-Ht-Uid:"140291685" RequestPath:"/virtual_pay/v2/live_voice/purchase_virtual_product" topic:prod* _stream:{service="traefik"} level:* |fields request_Authorization,request_X-Ht-Uid,RequestPath,request_Ali-Cdn-Real-Ip
```