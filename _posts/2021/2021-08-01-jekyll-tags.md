---
layout: mypost
title: 为jekyll搭建的blog生成tags页面
tags: jekyll
---

1. 目录
{:toc}

<!--more-->

## 创建tags.html文件

在博客根目录生成一个tags.html文件，内容如下：

```js
---
layout: page
permalink: /tags/
---

<ul class="tag-cloud">
{% for tag in site.tags %}
  <li style="font-size: {{ tag | last | size | times: 100 | divided_by: site.tags.size | plus: 70  }}%">
    <a href="#{{ tag | first | slugize }}">
      {{ tag | first }}
    </a>
  </li>
{% endfor %}
</ul>

<div id="archives">
{% for tag in site.tags %}
  <div class="archive-group">
    {% capture tag_name %}{{ tag | first }}{% endcapture %}
    <h3 id="#{{ tag_name | slugize }}">{{ tag_name }}</h3>
    <a name="{{ tag_name | slugize }}"></a>
    {% for post in site.tags[tag_name] %}
    <article class="archive-item">
      <h4><a href="{{ root_url }}{{ post.url }}">{{post.title}}</a></h4>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>
```

创建之后可以,可用通过root_url/tags来访问，比如我的就是`http://daotin.github.io/tags` 来访问tags页面



## 为每个post文章添加相关的tag链接

新建一个文件`_include/post-tag.html`内容如下:

```js
<div class="post-tags">
  Tags: 
  {% if post %}
    {% assign tags = post.tags %}
  {% else %}
    {% assign tags = page.tags %}
  {% endif %}
  {% for tag in tags %}
  <a href="/tags#{{tag|slugize}}">{{tag}}</a>{% unless forloop.last %},{% endunless %}
  {% endfor %}
</div>
```

在_layout/post.html中想要添加相关tag链接的地方加入代码`{% include post-tags.html %}`：

我的_layout/post.html修改的内容如下:

```js
   <h1 class="post-title" itemprop="name headline">{{ page.title }}</h1>
       {% include post-tag.html %}
```



## 添加css配置

需要添加的内容如下：

```css
// for tag cloud and archives

.tag-cloud {
  list-style: none;
  padding: 0;
  text-align: justify; 
  font-size: 16px;
  li {
    display: inline-block;
    margin: 0 12px 12px 0; 
  }
}
#archives {
  padding: 5px;
}
.archive-group {
  margin: 5px;
  border-top: 1px solid #ddd;
}
.archive-item {
  margin-left: 5px;
}
.post-tags {
  text-align: right;
}
```



## 效果

标签页效果：

![](/image/13.png)



原文链接：[http://qqailab.cn/2016/01/03/jekyll-tags-page.html](http://qqailab.cn/2016/01/03/jekyll-tags-page.html)



（完）

