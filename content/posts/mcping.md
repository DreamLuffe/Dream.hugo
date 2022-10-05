---
title: "NodeJS 获取 Minecraft 服务器状态"
date: 2022-10-05T07:25:08Z
tags:
- 我的世界
- Minecraft
- NodeJS
categories:
- 技术
---
# 前言
主要是由于 https://mcsrvstat.us/ 该网站无法使用之后又不想上游戏看**服务器是否开启**偷懒想出的办法。

## 步骤
打开 NodeJS 界面安装 NPM 包 [minecraft-server-ping](https://www.npmjs.com/package/minecraft-server-ping)
```shell
npm install -g minecraft-server-ping
```
安装完成后运行以下命令,之后就可以看见相关输出了
```bash
Welcome to Node.js v14.17.0.
Type ".help" for more information.
> const ping = require("minecraft-server-ping")
undefined
> let status = ping.ping("mc.hypixel.net",25565);
> status.then(res=>{
... console.log(res)})
```
如果服务器未开启则会出现以下错误
```plain
> (node:3796) UnhandledPromiseRejectionWarning: Error: connect ECONNREFUSED 127.0.0.1:25565
    at TCPConnectWrap.afterConnect [as oncomplete] (net.js:1146:16)
    at TCPConnectWrap.callbackTrampoline (internal/async_hooks.js:134:14)
```
