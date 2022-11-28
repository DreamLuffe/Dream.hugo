---
title: "使用 Javascript 解析 IDEA 书签为 Markdown 文本"
date: 2022-11-28T07:33:06Z
keywords: ["Idea","Markdown","nodejs"]

tags:
- Idea
- Javascript
- Markdown
- NodeJs
categories:
- 技术
---
# 介绍
本篇文章主要是为了方便本人在撰写 `README` 等相关文档时插入具体代码地址。

## 准备
首先你需要找到您的 `Idea书签` 文件,通常在目录 ` C:\Users\<用户名>\AppData\Roaming\JetBrains\IntelliJIdea<版本>\workspace` `.xml` 文件当中！  
我们需要复制以下结构
```xml
<option name="bookmarks">
    <BookmarkState>....</BookmarkState>
    <BookmarkState>....</BookmarkState>
    <BookmarkState>....</BookmarkState>
</option>
```

## 代码
```js
const XmlReader = require('xml-reader');

const reader = XmlReader.create({
    emitTopLevelOnly: false
});
const xml =
    `<option name="bookmarks">
          <BookmarkState>
            <attributes>
              <entry key="url" value="/shop-app/src/main/java/com/modules/order/rest/StoreOrderController.java" />
              <entry key="line" value="259" />
            </attributes>
            <option name="description" value="@ApiOperation(value = 订单详情,notes = 订单详情)" />
            <option name="provider" value="com.intellij.ide.bookmark.providers.LineBookmarkProvider" />
          </BookmarkState>
          <BookmarkState>
            <attributes>
              <entry key="url" value="/shop-app/src/main/java/com/modules/order/rest/StoreOrderController.java" />
              <entry key="line" value="306" />
            </attributes>
            <option name="description" value="@ApiOperation(value = 订单评价,notes = 订单评价)" />
            <option name="provider" value="com.intellij.ide.bookmark.providers.LineBookmarkProvider" />
          </BookmarkState>
        </option>`;
var markDownArr = {}
reader.on('tag:BookmarkState', (data) => {
    var description = data.children[1].attributes.value;
    var children = data.children[0];
    var javaFile = children.children[0].attributes.value;
    var line = children.children[1].attributes.value;
    var host = "https://gitee.com/ayou/shop-app/blob/master";
    var url = host + javaFile + "#L" +line
    var formatDesc = description.split(",")[0].split(" ")[2];
    var fileName = javaFile.split("/")[javaFile.split("/").length - 1].split(".")[0];
    var markdown = "- ["+formatDesc+"]("+url+")  "
    if (markDownArr[fileName] != undefined){
        markDownArr[fileName].push(markdown);
    }else{
        let arr = []
        arr.push(markdown);
        markDownArr[fileName] = arr;
    }
});
xml.split('').forEach(char => reader.parse(char));

for (let key in markDownArr) {
    console.log("###",key)
    markDownArr[key].forEach(v=>{
        console.log(v);
    })
}
```
结果
```markdown
### StoreOrderController
- [订单详情](https://gitee.com/ayou/shop-app/blob/master/shop-app/src/main/java/com/modules/order/rest/StoreOrderControl
ler.java#L259)
- [订单评价](https://gitee.com/ayou/shop-app/blob/master/shop-app/src/main/java/com/modules/order/rest/StoreOrderControl
ler.java#L306)
```

:(fa-brands fa-github):[源码](https://github.com/MochiParty/idea-bookmark-convert)
