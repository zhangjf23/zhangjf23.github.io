---
layout: page
title: About
description: 微胖男会改变世界
keywords: 个人介绍
comments: true
menu: 关于
permalink: /about/
---

我是张俊峰，融颜值与实力于一身。

坚信微胖是最完美的形态。

坚信熟能生巧，努力改变人生。

## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
