---
title: "保姆级教程：基于Docker部署雨中冒险2服务器"
date: 2022-10-17T10:30:15Z
keywords: ["雨中冒险2","ror2","rainofrisk2"]
summary: "一条命令解决雨中冒险2开服，Mod安装、中文语言配置"
featuredImage: "/posts/images/ror2/Risk_of_Rain_2_cover.jpg"

tags:
- Docker
categories:
- 技术
---
{{< admonition >}}
阅读此篇文章需要您有一定的 **Docker** 基础,当然如果你不了解也没有关系，可以查看 [简易开服](#easy)  
本篇文章所使用镜像源码 [:(fa-brands fa-github): ror2-server](https://github.com/avivace/ror2-server)
{{< /admonition >}}

配置需求
- 至少 2GB 内存
- 3GB 硬盘空间

## 简易开服 <a id="easy"></a>
```bash
docker run --name ror2 -d -e R2_HOSTNAME=雨中冒险2 -e R2_HEARTBEAT=1 \
-p 27015:27015/udp -p 27016:27016/udp avivace/ror2server:latest
```
运行以下命令之后，我们需要耐心等待 5-10分钟,在此期间容器会自动下载开服相关资源,我们需要耐心等待。  
输入 `docker logs -f -t ror2` 可以实时查看日志信息,出现以下信息就代表我们开服成功了，现在进入联机大厅可以看见一个名为 **雨中冒险2** 的服务器。
```text
Press Enter to chat. 
(Filename: C:\buildslave\unity\build\Runtime/Export/Debug/Debug.bindings.h Line: 39)

Steamworks Server IP discovered. 
(Filename: C:\buildslave\unity\build\Runtime/Export/Debug/Debug.bindings.h Line: 39)
```

### 相关参数

- `R2_PLAYERS`, 最大玩家数量, 默认为 4，不建议超过 16;
- `R2_HEARTBEAT`, 设置为 `1` 可以让 **多人联机列表显示您的服务器**, 必须添加 `-p 27016:27016/udp` 参数开放端口;
- `R2_HOSTNAME`, 设置您的服务器名称;
- `R2_PSW`, 加入服务器的密码，不填则无密码;
- `R2_ENABLE_MODS`, 设置为 `1` 开启 Mod 支持 [详细点我](#mod-support));
- `R2_QUERY_PORT`, Steamworks 监听的端口, 需要添加 `-p <PORT>:<PORT>/udp` 参数;
- `R2_SV_PORT`, 游戏服务器端口，玩家加入用的, 需要添加 `-p <PORT>:<PORT>/udp` 参数.
- `R2_TAGS`, 服务器标签，方便查询，一般不填.
- `R2_GAMEMODE`, 游戏模式, 默认经典 `ClassicRun`. 其他模式:
    - `ClassicRun` (Standard)
    - `InfiniteTowerRun` (Simulacrum)

#### 检测服务器状态API
`<IP_ADDRESS>` 变量替换为您的服务器 `公网IP` 加默认端口 `27016`
```bash
curl http://api.steampowered.com/ISteamApps/GetServersAtAddress/v0001/?format=json&addr=<IP_ADDRESS>
```

## Mod 支持 <a id="mod-support"></a>
安装 Mod 前你需要拥有以下文件,这里推荐使用 [**r2modman**](https://thunderstore.io/package/ebkr/r2modman/) 进行安装
- **BepInEx** 
- `doorstop_config.ini` 和 `winhttp.dll`，位于 BepInEx 同级目录
 
之后我们需要将该文件目录,您的模组包目录,这里用 `/path/to/directory` 代替，使用命令
```bash
docker run --name ror2 -d -v /path/to/directory:/root/ror2ds-mods -e R2_ENABLE_MODS=1 \
-e R2_HEARTBEAT=1-p 27015:27015/udp -e R2_HOSTNAME=雨中冒险2 \
-p 27016:27016/udp avivace/ror2server:latest
```
耐心等待 `Steamworks Server IP discovered. ` 出现即可！至此安装 Mod 就完成了。

## 可能遇到的问题

### 中文玩家名为问号
暂时没找到解决办法

### 设置中文语言
创建一个文件 `config.cfg` 
```bash
echo "language zh-CN;" > config.cfg
```
Docker 启动加上以下参数
```bash
-e LANG=zh_CNN.UTF-8 -e LANGUAGE=zh_CN.UTF-8:zh_CN -v config.cfg:/root/ror2-dedicated/Risk of Rain 2_Data/Config
```

### 服务器标签
|名称|说明|
|:---|:---|
|mod|是否启用 mod|
|rv|是否开启投票 1 开启 0 关闭|
|dz|细雨难度|
|rs|暴雨难度|
|mn|季风难度|

|神器标签|说明|
|:---|:---|
|a=|无神器|
|0|恶意|
|1|统率|
|2|荣耀|
|3|谜团|
|4|混沌|
|5|玻璃|
|6|纷争|
|7|进化|
|8|蜕变|
|9|牺牲|
|:|复仇|
|;|亲族|
|<|虫群|
|=|亡者|
|>|脆弱|
|?|灵魂|

![server info](/posts/images/ror2/server_info.png)
