---
title: "Quarkus | 自定义额外配置文件/数据源"
date: 2023-01-06T14:33:38Z
keywords: ["Quarkus","Java","Quarkus.io"]
comment: true

tags:
- Java
- Quarkus
- 云原生
categories:
- Java

featuredImage: /posts/quarkus/images/quarkus-logo.jpeg

---
{{< admonition >}}
本文主要是个人使用 `Quarkus` 框架开发项目所得出的经验,如有错误还请指正
{{</ admonition >}}

> 相关文档  
> - https://quarkus.io/guides/config-reference  
> - https://quarkus.io/guides/config-extending-support  
> - https://quarkus.io/guides/config-mappings
> - :(fa-brands fa-github): [源码](https://github.com/Ayouuuu/quarkus-example/tree/master/extend-config)

# 前言
`Quarkus` 默认可以加载**7**种数据源,如下表格所示. 现在我想用 `lang.properties` 专门用来配置语言文件,而不是 `application.properties` 存储冗长的语言文件,所以 `自定义数据源` 就是解决问题的答案.

|ConfigSource|Ordinal|
|:---|:---|
|System Properties|400|
|Environment Variables from System|300|
|Environment Variables from `.env` file|295
|InMemoryConfigSource|275|
|`application.properties` from `/config`|260|
|`application.properties` from application|250|
|`microprofile-config.properties` from application|100|

## 项目准备
使用 `IDEA` 创建 `Quarkus` 项目并加载相关 `lib` 依赖  
![idea-1](/posts/quarkus/images/extend-config/extend-config-idea-1.jpg)  
![idea-2](/posts/quarkus/images/extend-config/extend-config-idea-2.jpg)  

### 数据源代码
现在我们要加载 `properties` 文件在 `src/main/java/` 目录创建 `java` 文件,然后继承 `ApplicationPropertiesConfigSourceLoader` 以及实现接口 `ConfigSourceProvider`,之后在方法 `getConfigSources` 添加需要加载的数据源,具体原理可以自行查看源码了解.如果需要加载 `yaml` 文件,需要 `maven` 引入 [quarkus-config-yaml](https://quarkus.io/guides/config-yaml) 依赖,并且 `Loader `要改成 `ApplicationYamlConfigSourceLoader`然后添加 `yaml`/`yml` 文件加载代码即可!  
```java
package com.ayou.source;

import io.quarkus.runtime.configuration.ApplicationPropertiesConfigSourceLoader;
import org.eclipse.microprofile.config.spi.ConfigSource;
import org.eclipse.microprofile.config.spi.ConfigSourceProvider;

import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.List;
/**
*    加载 lang.properties 文件
*/
public class LangPropertiesConfigSource extends ApplicationPropertiesConfigSourceLoader implements ConfigSourceProvider {
    @Override
    public Iterable<ConfigSource> getConfigSources(ClassLoader classLoader) {
        List<ConfigSource> sourceList = new ArrayList<>();
        // ordinal 为优先级 越高越优先加载
	// sources 支持 http jar file
        sourceList.addAll(this.loadConfigSources("lang.properties",110,classLoader));
        sourceList.addAll(this.loadConfigSources(Paths.get(System.getProperty("user.dir"),"config","lang.properties").toUri().toString(),120,classLoader));
        return sourceList;
    }
}
```
```java
package com.ayou.source;

import io.quarkus.config.yaml.runtime.ApplicationYamlConfigSourceLoader;
import org.eclipse.microprofile.config.spi.ConfigSource;
import org.eclipse.microprofile.config.spi.ConfigSourceProvider;

import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.List;
// 加载 yaml 文件
public class LangYamlConfigSource extends ApplicationYamlConfigSourceLoader implements ConfigSourceProvider {
    @Override
    public Iterable<ConfigSource> getConfigSources(ClassLoader classLoader) {
        List<ConfigSource> sourceList = new ArrayList<>();
        // ordinal 为优先级 越高越优先加载
        sourceList.addAll(this.loadConfigSources("lang.yml",110,classLoader));
        sourceList.addAll(this.loadConfigSources(Paths.get(System.getProperty("user.dir"),"config","lang.yml").toUri().toString(),120,classLoader));
        return sourceList;
    }
}
```

### 注册数据源

写完代码之后,我们还需要做一件事情,就是 `注册数据源`,不然无法加载文件. 这一步我们需要这 `resource` 文件夹下创建 `META-INF/service/org.eclipse.microprofile.config.spi.ConfigSourceProvider` 文件写入 `数据源` 的路径, 这样之后就完成了 `数据源` 的加载,现在让我们写入一些 `文本信息` 进去吧!
```text
com.ayou.source.LangPropertiesConfigSource
com.ayou.source.LangYamlConfigSource
```

### 测试
在 `resources` 目录下创建相关文件 `lang.properties` / `lang.yml` 根据相关语法写入一些内容.  
测试详情可见 [ConfigSourceTest](https://github.com/Ayouuuu/quarkus-example/blob/master/extend-config/src/test/java/com/ayou/CustomSourceTest.java)  
![index](/posts/quarkus/images/extend-config/config-index.jpg)
