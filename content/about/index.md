---
title: "建站日志"
subtitle: ""
date: 2022-09-17T16:20:25Z
draft: false
description: ""
keywords: 
- 建站
- 日志
summary: "关于我建站的开始"
comment: false
weight: 0

tags:
- 日志
categories:
- 日志

resources:
- name: featured-image
  src: featured-image.jpg
- name: featured-image-preview
  src: featured-image-preview.jpg
---


<a href="https://github.com/Ayouuuu/Ayouuuu.hugo">
<img src="https://ghchart.rshah.org/Ayouuuu.svg" title="Ayou's Github chart" alt="Ayou's Github chart" style="width:100%;"/>
</a>
{{< admonition quote ":(fa-regular fa-clock): 2022-09-18 00:00:00" >}}
{{< typeit group=expect >}}
  弱小和无知，都不是生存的障碍，傲慢才是。  ——《三体》
{{< /typeit >}}

{{< /admonition >}}

#### [提交日志](https://github.com/Ayouuuu/Ayouuuu.hugo/commit/master) 
>
<script>
fetch('https://api.github.com/repos/Ayouuuu/Ayouuuu.hugo/commits')
  .then(res => res.json())
  .then(res => {
  	res.forEach(value=>{
		let p = document.createElement("p")
		p.innerHTML = "<a href="+value.html_url + ">" +value.sha.slice(0,6) + "</a> " + value.commit.message
		document.getElementsByTagName("blockquote")[0].appendChild(p)
	})
  })
</script>
