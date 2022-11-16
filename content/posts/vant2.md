---
title: "Tomcat 部署 Vant2-UI 官方文档"
date: 2022-11-16T01:52:10Z
keywords: ["Tomcat","VantUI"]

tags:
- Tomcat
- VantUI
categories:
- 技术

featuredImage: /posts/images/vant.jpg

---

> 提前需要准备的东西    
> - [Tomcat](https://tomcat.apache.org/)  
> - [VantUI 2.x](https://github.com/youzan/vant/tree/2.x)  
> - [NodeJS/NPM](https://nodejs.org/zh-cn/)
> - [Git](https://git-scm.com/)


## 操作步骤
首先使用 `Git` 将源代码下载至本地  
```bash
git clone https://github.com/youzan/vant.git		# Github
git clone https://gitee.com/vant-contrib/vant.git	# 码云镜像 
```
进入 `vant` 目录，切换 `2.x` 分支
```bash
cd vant
git checkout 2.x
```

### 安装依赖
```bash
npm install -g yarn	# 安装 yarn
yarn config set registry https://registry.npm.taobao.org	# 设置源
yarn	# 下载依赖
```
### 配置
#### package.json

依赖下载完之后需要修改 `package.json` 文件，加入一个新的脚本 `build:site`
```json
  "scripts": {
    "build:site": "vant-cli build-site"
  }
```

#### vant.config.js
然后修改 `vant.config.js` 中的 `publicPath` 为 `/vant-ui/`
```js
    site: {
      publicPath: process.env.PUBLIC_PATH || '/vant-ui/',
    }
```

### 打包

修改文件后保存，开始打包文档代码,打包好的文件保存在 `site` 目录中
```bash
[root@VM-4-9-centos vant]# npm run build:site

> vant@2.12.52 build:site
> vant-cli build-site


✔ Vant Cli
  Compiled successfully in 20.30s

Browserslist: caniuse-lite is outdated. Please run:
npx browserslist@latest --update-db
```

## Tomcat 部署
进入 **Tomcat** `webapps` 目录创建文件夹 `vant-ui`(和上面修改的 `publicPath` 对应),将打包好的 `site` 中的文件放入新建的 `vant-ui` 中即可，开启 **Tomcat** 访问 [http://localhost:8080/vant-ui](http://localhost:8080/vant-ui)。
