---
title: "从零开始构建 Hugo 博客"
date: 2022-09-22T15:35:39Z
tags:
- Hugo
- 总结
categories:
- 总结
---

{{< center-quote >}}

{{<typeit>}}

“理解一个东西最好的方式,就是把他讲出来.” 

{{</typeit>}}

{{</center-quote>}}

# 前言

自从使用 `WordPress` 博客数据库被脱库导致以前的内容没有了之后，才让我意识到搭建一个静态博客的重要性，于是在九月初的时候花费了两周搭建一个新的博客，其一是我的技术在脱库之后的几个月有了较大的进步，对静态网站托管等有了一定的认识；其二是 `Github` 存储加上 `Netlify` 静态托管，省去了中间很多事情；其三这也是个人喜欢折腾的一种表现吧；同时也是撰写这篇总结的目的，希望能对您有所帮助:wink:。本篇文章包含:

- Hugo CLI 的使用
- Hugo 主题的选择
  - [Fixit](https://github.com/hugo-fixit/FixIt) 主题的配置
- Github 存储 Hugo 项目
- Netlify 自动化部署 Hugo
  - HTTPS 配置

## Hugo CLI 安装及其使用

{{<admonition note>}}

需要您的电脑拥有 `Docker` 容器环境，同时也需要对 Docker 有一定理解

`Linux` 系统不是必须的，`Windows`上使用 Docker 也是同理 

[Hugo官方文档](https://gohugo.io/documentation/)

[FixIt主题配置](https://fixit.lruihao.cn/zh-cn/)

{{</admonition>}}

使用以下命令可以创建一个 Hugo CLI 的容器环境，退出后容器自动删除，可以说是非常方便了，`$(pwd)`为当前输入的目录(Windows 请自行修改).

```bash
sudo docker run --rm -it -p 1313:1313 -v $(pwd):/src klakegg/hugo:0.101.0-ext-alpine shell
```

进入容器环境后可以参照[官方文档](https://gohugo.io/getting-started/quick-start/#step-2-create-a-new-site)新建一个 **Site**

```sh
hugo:/src/test$ hugo new site quickstart
Congratulations! Your new Hugo site is created in /src/test/quickstart.
```

添加一个主题，这里使用 [Fixit](https://github.com/hugo-fixit/FixIt) 

```sh
cd quickstart
git init
git submodule add https://github.com/hugo-fixit/FixIt.git themes/FixIt # 添加子模块
sudo cp themes/FixIt/config.toml config.toml # 复制主题配置到本地
```

修改主题配置，具体配置可以[参考项目](https://github.com/Ayouuuu/Ayouuuu.hugo/blob/master/config.toml),FixIt 主题[文档](https://fixit.lruihao.cn/zh-cn/)

```text
baseURL = "你的网站URL"
defaultContentLanguage = "zh-cn"
languageCode = "zh-CN"
hasCJKLanguage = true
title = "网站标题"
enableRobotsTXT = true
enableEmoji = true
enableGitInfo = true
relativeURLs = false
buildDrafts = false
summaryLength = 150
# 主题
theme = "FixIt"
```

### 启动 Hugo

当我们配置完 `主题`、`配置` 在容器里面输入以下命令后使用浏览器进入 **[localhost:1313](http://localhost:1313)** 预览

```sh
hugo server # 启动服务器
hugo server -D -e production # 启用预览文章显示、正式环境
```

## Github 存储博客项目

这一部分就不详细说明了,就是新建一个 Github 仓库将项目上传到远程仓库中,具体还是说一下如何部署 Netlify

## Netlify 静态部署



首先在 Hugo 项目根目录创建文件 `netlify.toml`

```pro
[build]
  command = "hugo"
  functions = "netlify/functions"
  publish = "public"

[build.environment]
  HUGO_VERSION = "0.101.0"

[[headers]]
  for = "/*"
  [headers.values]
    cache-control = "max-age=7200"
```

之后进入 [Netlify](https://app.netlify.com/) 使用 Github 登陆,新建一个 `Teams` 找到 `Sites`->`Add new site`->`Import an existing project `选择你的 Github 项目，点击 `Deploy site` 即可完成网站搭建，非常的方便！

![image-20220923155133974](https://pic.ayou10031.cn/public/image-20220923155133974.png)

### 自定义域名/HTTPS配置

进入 `Domain settings`->`Add custom domain`输入域名相关信息，之后要添加 DNS 解析，点击自定义域名的 `Check DNS configuration`,找到你的域名服务商控制台，将你的域名信息使用 `CNAME` 解析到相关地址，然后等待即可,HTTPS会在 DNS 配置完成之后自动配置，大约十分钟左右.

之后的每一次 Github 提交 Netlify 都会直接部署，非常方便.

# 结语

这篇总结可能相对来说不是很全面，也是受限于本人的文笔不太干练，有些内容可能仅仅是一笔带过，所有有什么不了解的地方还请留言:smile:
