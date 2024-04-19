# Discordxy Proxy

代理 MidJourney、Pika的discord频道，实现api形式调用AI绘图、文生视视频和图生视频

本程序以golang开发，高效协程队列代理程序，自动断线重连机制等，数据存储目前可使用内存与Redis存储，默认30天有效期，可在配置自行配置

## MidJourney主要功能
- [x] 支持 Imagine 指令和相关动作(U1 U2 U3 U4 V1 V2 V3 V4 Reload重绘)
- [x] 支持多账号配置，每个账号可设置对应的任务队列
- [x] 其它功能开发中

## Pika主要功能
- [x] 支持文生视频
- [x] 支持图生视频
- [x] 支持多账号配置，每个账号可设置对应的任务队列

## Didcord Token获取方式

 <img src="https://github.com/felixcky/Discordxy-proxy/blob/master/user-token.jpg?raw=true" alt="user-token"/>
