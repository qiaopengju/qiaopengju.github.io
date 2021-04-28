---
title: Hexo note
hide: false
math: false
mermaid: false
date: 2021-04-28 16:32:28
categories: Blog
tags:
index_img:
banner_img:
comment: true
excerpt: Here are some hexo notes.
sticky: 100
---

<!-- more -->

#### 置顶：

在Front-matter中加入:

```markdown
$ sticky: 100 <!--数字越大排序越前-->
```

---

#### 加载html：

* 在md[^1]中加入:

```html
<iframe src="filename.html" width="700" height="800"></iframe>
```

* 要避免hexo渲染此html，在_config.yml写入:

```yml
skip_render:
	'_posts/文件夹名/文件名.html'
```

---

#### 加载pdf

使用hexo-pdf插件，下载：

```shell
$ npm install hexo-pdf --save
```

md插入pdf：

```markdown
{% pdf ./filename.pdf %}
```

---

#### 勾选框

```markdown
{% cb text, checked?, inline? %}
<!--first line--> {%cb true} Completed
<!--second line--> {%cb false} In progress
```

{% cb true %} Completed

{% cb false %} In progress

---

#### 脚注|参考

This is a footnote[^2].

[^1]:markdown
[^2]: foot note