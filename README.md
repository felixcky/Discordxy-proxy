# Discordxy Proxy

代理 MidJourney、Pika的discord频道，实现api形式调用AI绘图、文生视视频和图生视频,可配置非法词过滤，下载配置好即可使用，省去繁琐的代理开发工作

建议该程序IP和端口只开启内网访问权限，原则上不建议API接口对外网访问，可用另一对外访问接口服务端程序进行内网调用后以自己的授权逻辑进行对外开放相关接口

本程序以golang开发，高效协程队列代理程序，自动断线重连机制等，数据存储目前可使用内存与Redis存储，存储默认30天有效期，可在配置自行配置

## 使用

linux

```shell

chmod +x disxy

./disxy

```

windows

```shell

disxy.exe

```

`默认配置访问地址为：http://127.0.0.1:9394`


## MidJourney主要功能
- [x] 支持 Imagine 相关指令操作（选择、变换、重绘、微调、扩图等）
- [x] 一层指令：U1 U2 U3 U4 V1 V2 V3 V4 Reroll
- [x] 二层指令：UpscaleSubtle UpscaleCreative VarySubtle VaryCreative Zoom2x Zoom1d5x PanLeft PanRight PanUp PanDown （区域重绘和自定义扩图暂不支持）
- [x] 支持任务实时进度
- [x] 支持多账号配置，每个账号可设置对应的任务队列
- [x] 其它功能陆续开发中

## Pika主要功能
- [x] 支持文生视频
- [x] 支持图生视频
- [x] 支持多账号配置，每个账号可设置对应的任务队列

## Didcord Token获取方式

登录Discord后，随便进入自己的频道或别人的，按浏览器F12打开开发者工具，搜索message，找到下图所示，复制token到配置文件

 <img src="https://github.com/felixcky/Discordxy-proxy/blob/master/user-token.jpg?raw=true" alt="user-token"/>

## Didcord的服务器ID（guild-id）和频道ID（channel-id）获取方式

MidJourney 先加入官方频道，然后再创建自己的私有服务频道，并在自己的服务器添加MidJourney bot机器人

Pika不允许在自己的服务器添加，请选加入pika服务器频道后再获取，进入务器频道后，找到 【CREATIONS->generate-n】 n表示1-10自行选择一个

而你进入当前的频道后你浏览器上的连接如：

```
https://discord.com/channels/1123665496148017235/1134375328442224690

1123665496148017235表示：guild-id

1134375328442224690表示：channel-id

```

## 修复更新

2024-05-09 修复MidJourney版本更新

## Config.json

```json

{
  "http": {
    "server": "127.0.0.1",
    "port": "9394"
  },
  "discord": {
      "proxy-server": "https://discord.com",
      "proxy-cdn": "https://cdn.discordapp.com",
      "proxy-wss": "wss://gateway.discord.gg",
      "upload-server": "https://discord-attachments-uploads-prd.storage.googleapis.com",
      "notify-pool-size": 10,
      "task-storage": "memory",
      "redis": {
        "host": "127.0.0.1",
        "password": "123456",
        "port": 6379,
        "database": null
      },
      "accounts": [
        {
          "user-token": "",
          "user-agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.30 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.30",
          "enable": true,
          "timeout-minutes": 10,
          "action":[
            {
              "name": "mj",
              "guild-id": "",
              "channel-id": "",
              "core-size": 3,
              "queue-size": 10
            },
            {
              "name": "pika",
              "guild-id": "",
              "channel-id": "",
              "core-size": 2,
              "queue-size": 3
            }
          ]
        },
        {
          "user-token": "",
          "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36",
          "enable": false,
          "timeout-minutes": 10,
          "action":[
            {
              "name": "pika",
              "guild-id": "",
              "channel-id": "",
              "core-size": 1,
              "queue-size": 1
            }
          ]
        }
      ]
  }
}

```

**配置参数**
| 参数      | 类型     | 说明  |
| ------- | ------ | ----------- |
| http.server    | string    | 服务IP|
| http.port    | string    | 服务端口|
| discord.proxy-server    | string    | 代理服务地址|
| discord.proxy-cdn    | string    | 代理cdn地址|
| discord.proxy-wss    | string    | 代理websocket地址|
| discord.upload-server    | string    | 代理上传地址|
| discord.task-storage    | string    | 储储方式：redis、memory|
| discord.redis    | array    | redis配置|
| discord.accounts    | list    | 帐号池列表|
| discord.accounts[].user-token    | string    | 帐号token|
| discord.accounts[].enable    | bool    | 是否启用|
| discord.accounts[].timeout-minutes    | int    | 超时时间（单位：分钟）|
| discord.accounts[].action   | list    | 帐号功能列表|
| discord.accounts[].action[].name   | string    | 功能名：mj、pika|
| discord.accounts[].action[].guild-id   | string    | 服务器id|
| discord.accounts[].action[].channel-id   | string    | 频道id|
| discord.accounts[].action[].core-size   | int    | 并发任务数量|
| discord.accounts[].action[].queue-size   | int    | 任务队列数量|


## 通用接口

### 1.1 查询代理帐号在线

`GET` /client/online


## MidJourney接口Api

### 2.1 绘图

`POST` /mj/imagine

**请求格式：Body json**

```json

//文本
{"prompt":"a small cat"}

//垫图加入url
{"prompt":"http://www.xxx.com/1.jpg runing on the sky"}

```

**返回结果**
```json
{
    "code": 0,
    "result": "1211421713496994992", //任务taskId
    "message": "Success"
}
```

### 2.2 选择与变化

**请求格式：Body json**

`POST` /mj/change

| 参数      | 类型     | 说明  |
| ------- | ------ | ----------- |
| taskId    | string    | 任务ID |
| action    | string    | Action操作指令 |

| 参数       | 说明  |
| ------- | ----------- |
| | 一层指令 |
| U1    | 选择图1 |
| U2    | 选择图2 |
| U3    | 选择图3 |
| U4    | 选择图4 |
| V1    | 变换图1 |
| V2    | 变换图2 |
| V3    | 变换图3 |
| V4    | 变换图4 |
| Reroll    | 重绘 |
| | 二层指令 |
| UpscaleSubtle | 高清（微调） |
| UpscaleCreative | 高清（创意） |
| VarySubtle | 变换（微调） |
| VaryCreative | 变换（创意） |
| Zoom2x | 扩图2倍 |
| Zoom1d5x | 扩图1.5倍 |
| PanLeft | 向左扩图 |
| PanRight | 向右扩图 |
| PanUp | 向上扩图 |
| PanDown | 向下扩图 |

```json

{"taskId":"1211421713496994992","action":"V4"}

```

**返回结果**

```json
{
    "code": 0,
    "result": "1211421713496994992", //任务taskId
    "message": "Success"
}
```

### 2.3 任务进度

`GET` /mj/status?taskId=1160181713497101114

**返回结果**

| 参数      | 类型     | 说明  |
| ------- | ------ | ----------- |
| action    | string    | 任务功能 Imagine=绘画 Upsample=选择 Variation=变化 Reroll=重画 |
| status    | string    | 任务状态 Submit=提交 Create=创建 Queuing=排队中 Running=运行中 Success=成功 Failed=创建失败 Timeout=任务超时|
| progress    | string    | 进度 |


```json

{
    "code": 0,
    "result": {
        "action": "Imagine",
        "channelId": "xxxxxxxxxx",
        "finalPrompt": "a small cat",
        "finishTime": 1713497139,
        "imageRawUrl": "https://cdn.discordapp.com/attachments/xxxx/xxx/xxx._a_small_cat_4e68485a-900c-4c1b-9222-f9b5ae4b3fcf.png?ex=66345934&is=6621e434&hm=f9948a08abccc78866b0ec2d52e3d0da7a74ade2ea609ccc4c8268b18bc8e98a&",
        "imageUrl": "https://cdn.discordapp.com/attachments/xxx/xxx/cxxxx._a_small_cat_4e68485a-900c-4c1b-9222-f9b5ae4b3fcf.png?ex=66345934&is=6621e434&hm=f9948a08abccc78866b0ec2d52e3d0da7a74ade2ea609ccc4c8268b18bc8e98a&",
        "messageHash": "4e68485a-900c-4c1b-9222-xxxxxxxxx",
        "messageId": "1230720793068179496",
        "nonce": "xxxxxxxxxx",
        "progress": "100%",
        "prompt": "a small cat",
        "referenceMessageId": "xxxxxxxxxx",
        "startTime": 1713497101,
        "status": "Success",
        "submitTime": 1713497101
    },
    "message": "Success"
}

{
    "code": 0,
    "result": {
        "action": "Variation",
        "channelId": "xxxxxxxx",
        "finalPrompt": "a small cat - Variations (Strong)",
        "finishTime": 1713497139,
        "imageRawUrl": "https://cdn.discordapp.com/attachments/xxxx/xxx._a_small_cat_4e68485a-900c-4c1b-9222-f9b5ae4b3fcf.png?ex=66345934&is=6621e434&hm=f9948a08abccc78866b0ec2d52e3d0da7a74ade2ea609ccc4c8268b18bc8e98a&",
        "imageUrl": "https://cdn.discordapp.com/attachments/xxx/xxx/xxx._a_small_cat_4e68485a-900c-4c1b-9222-f9b5ae4b3fcf.png?ex=66345934&is=6621e434&hm=f9948a08abccc78866b0ec2d52e3d0da7a74ade2ea609ccc4c8268b18bc8e98a&",
        "messageHash": "4e68485a-900c-xxxx-9222-xxxxxxxxx",
        "messageId": "xxxxxxxxxx",
        "nonce": "xxxxxxxxx",
        "params": {
            "customId": "MJ::JOB::variation::4::3c833bd1-aaab-xxxx-xxxx-b55652ee5f11",
            "index": "4",
            "messageId": "xxxxxxxxxx"
        },
        "progress": "100%",
        "prompt": "a small cat",
        "referenceMessageId": "xxxxxxxxx",
        "startTime": 1713497101,
        "status": "Success",
        "submitTime": 1713497101
    },
    "message": "Success"
}

```


## Pika接口Api

### 3.1 Pika文生视频

`POST` /pika/create

**请求格式：Body json**

| 参数      | 类型     | 说明  |
| ------- | ------ | ----------- |
| prompt    | string    |提示词与句子（只能使用英文，不可使用中文，请先用GPT或其它翻译）|


```json

{"prompt":"A cute small cat"}

```

### 3.2 Pika图生视频

`POST` /pika/animate

**请求格式：Form Data**

| 参数      | 类型     | 说明  |
| ------- | ------ | ----------- |
| file    | file    | 上传图片，以file文件上传  |
| prompt    | string    |提示词与句子（只能使用英文，不可使用中文，先用GPT翻译）  |


### 3.3 获取视频任务状态

`GET` /pika/status?nonce=XXX


