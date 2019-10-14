# simplepro定制首页
simplepro首页默认了较多的模块，也许某些你无法用上。所以该文档提供了如何定制。

simpleui采用框架结构，simplepro也是基于simpleui，simpleui的首页文件home.html

只要重新该页面即可。

重新有两种方式，第一种全覆盖，第二种block方式

## 全覆盖方式
在你的项目的templates目录中建立以下文件结构
```
├─templates
│  ├─admin
│  │  ├─home.html
```
home.html内容：
```html
{% load i18n static simpletags %}
<link rel="stylesheet" href="{% static 'admin/simplepro/css/home.css' %}">
<script type="text/javascript" src="{% static 'admin/simplepro/echarts/echarts.min.js' %}"></script>
<div class="home-body">

     {% block quick %}
        {% include 'admin/parts/quick.html' %}
    {% endblock %}

    {% block chart_cards %}
        {% include 'admin/parts/charts_cards.html' %}
    {% endblock %}

    {% block line_chart %}
       {% include 'admin/parts/line_chart.html' %}
    {% endblock %}

    {% block info %}
       {% include 'admin/parts/info.html' %}
    {% endblock %}


    <div style="height: 80px"></div>
</div>
<el-backtop target=".home-body"></el-backtop>
```
你可以完全拷贝内容，到新建的home.html中，挑选需要的include部分。

## 继承block方式
也需要在templates->admin->home.html中建立文件
html内容：

```html
{% extends 'admin/home.html' %}
{% block quick %}
    //...这里重新你的内容
{% endblock%}

```
block方式文档请参考django的文档。