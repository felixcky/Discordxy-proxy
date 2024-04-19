# Discordxy Proxy

代理 MidJourney、Pika的discord频道，实现api形式调用AI绘图、文生视视频和图生视频

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

## MidJourney主要功能
- [x] 支持 Imagine 指令和相关动作(U1 U2 U3 U4 V1 V2 V3 V4 Reload重绘)
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

## 通用接口

### 1.1 查询代理帐号在线

`GET` /client/online


## MidJourney接口Api

### 2.1 绘图

`POST` /mj/submit/imagine

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

`POST` /mj/submit/change

| 参数      | 类型     | 说明  |
| ------- | ------ | ----------- |
| action    | string    | Action操作 U1、U2、U3、U4、R0、V1、V2、V3、V4 |

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

`GET` /mj/submit/status?taskId=1160181713497101114

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
            "customId": "MJ::JOB::variation::4::3c833bd1-aaab-42f4-89c0-b55652ee5f11",
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


