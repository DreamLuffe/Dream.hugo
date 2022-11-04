---
title: "记 Tomcat 部署 Spring Boot 项目验证码失效的解决流程"
date: 2022-11-03T01:37:36Z
keywords: ["Tomcat","Spring Boot","验证码","AccessDeniedException"]

tags:
- Tomcat
- Java
- ImageIO
categories:
- 技术
---

# 前言

本人在使用 `Ubuntu 20.14` 的系统,使用 `Tomcat` 部署 `Spring boot` 项目进入网页端一直无法正常显示验证码，经过一小时的查找，终于找到问题所在，以下是相关解决思路。
- 验证码lib [EasyCatpcha](https://mvnrepository.com/artifact/com.github.whvcse/easy-captcha)

## 思路1——修改启动脚本

有些网站说要在 `catalina.sh` 下面添加启动参数 `-Djava.awt.headless=true`,但是实测无用,直接开启 `Spring Boot` 项目可以访问到图片,但是部署到 `Tomcat` 则不行，判断为接口无误，为 `Tomcat` 问题，继续查找！
```bash
sed -i '/-Djava\.io\.tmpdir=/a\-Djava.awt.headless=true \\' catalina.sh
```

## 思路2——查找日志

进入目录 `/tomcat/logs` 找到 `catalina.out` 文件，仔细搜索发现问题所在——找不到文件夹 `temp`
```text
javax.imageio.IIOException: Can't create output stream!
	at javax.imageio.ImageIO.write(ImageIO.java:1574)
	at com.wf.captcha.ArithmeticCaptcha.graphicsImage(ArithmeticCaptcha.java:82)
	at com.wf.captcha.ArithmeticCaptcha.out(ArithmeticCaptcha.java:45)
	at com.wf.captcha.base.Captcha.toBase64(Captcha.java:127)
	at com.wf.captcha.ArithmeticCaptcha.toBase64(ArithmeticCaptcha.java:50)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
	at java.lang.Thread.run(Thread.java:750)
Caused by: javax.imageio.IIOException: Can't create cache file!
	at javax.imageio.ImageIO.createImageOutputStream(ImageIO.java:423)
	at javax.imageio.ImageIO.write(ImageIO.java:1572)
	... 118 more
Caused by: java.nio.file.NoSuchFileException: /opt/tomcat/temp/imageio5346868868735294464.tmp
	at sun.nio.fs.UnixException.translateToIOException(UnixException.java:86)
	at sun.nio.fs.UnixException.rethrowAsIOException(UnixException.java:102)
```
### 解决办法

在 `Tomcat` 根目录创建文件夹 `temp` 即可！

### EasyCatpcha 源码解析
`com.wf.captcha.ArithmeticCaptcha`
```java
    private boolean graphicsImage(char[] strs, OutputStream out) {
        try {
            BufferedImage bi = new BufferedImage(this.width, this.height, 1);
            Graphics2D g2d = (Graphics2D)bi.getGraphics();
	    ......
            g2d.dispose();
            ImageIO.write(bi, "png", out); 
	    # 这一步写入图片数据到文件夹，因为找不到文件夹，所以返回的数据只有 data:image/png;base64,
	    # 详细的可以自行翻看源码，具体就是有一个 CacheDirectory 对应 tomcat/temp 文件夹
            out.flush();
            boolean var20 = true;
            return var20;
        } catch (IOException var18) {
            var18.printStackTrace();
        } finally {
            try {
                out.close();
            } catch (IOException var17) {
                var17.printStackTrace();
            }

        }

        return false;
    }
```

