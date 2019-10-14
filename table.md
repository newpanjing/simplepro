# simplepro 表格
simplepro table基于elementui table，设置主要来自ModelAdmin，写法与原生admin一致，但是在原生的基础上增加了和减少了部分字段。注意目前table仅支持下列这些字段，但是在编辑和添加页还有用到其他字段，请参考原生admin与simpleui，simplepro是完全基于simpleui的。

# ModelAdmin部分

## 自定义按钮

admin中的自定义actions也是支持的，也就是simpleui说说的自定义按钮。

定义方法和使用均与原生admin一致，不过simpleui在这基础上进行了扩展。可以参考[自定义按钮文档](./action.md)

## 字段
|字段|类型|说明|
|---|---|---|
|list_display|tuple|table显示的字段|
|search_fields|tuple|搜索框搜索的字段|
|list_per_page|int|每页显示的数量|
|list_filter|array|筛选字段|
|fields_options|dict|表格的表头字段设置|
|actions_show|boolean|显示隐藏action，默认为True，只有显式指定为False的时候才隐藏|
|actions|array|[自定义按钮](#自定义按钮)|


### fields_options字段
admin 中字段设置，及时list_display 未定义该字段，在这里设置了也不会出错
表头字段完全遵循elementui的table文档：

[https://element.eleme.cn/#/zh-CN/component/table](https://element.eleme.cn/#/zh-CN/component/table)

fields_options支持自定义字段的值，比如model中的自定义方法 就算自定义字段

### 目前支持以下属性

|字段|类型|说明|
|---|---|---|
|fixed|str|固定列，取值 left 和right|
|sortable|boolean|排序字段，默认为'custom'|
|width|str|宽度，取值例如100px|
|min_width|str|最小宽度，取值例如100px|
|resizable|boolean|列是否可以调整宽度|
|class_name|str|列的 className|
|label_class_name|str|当前列标题的自定义类名|

+ sortable字段

排序字段，默认为'custom'，取值为：	true, false, 'custom' 

其中为custom的时候网络排序

取值为true，本地排序

自定义字段该值始终为false不排序

### fields_options例子

```python
fields_options = {
        'id': {
            'fixed': 'left',
            'width': '80px',
            'align': 'center'
        },
        'create_time': {
            'fixed': 'right',
            'width': '200px',
            'align': 'left'
        }
    }
```

## 方法

|字段|类型|说明|
|---|---|---|
|delete_queryset|function|删除时触发该方法|
|formatter|function|数据格式化|
|get_queryset|function|获取自定义的queryset|
|get_list_display|function|获取自定义的list_display|
|get_list_filter|function|获取自定义的list_filter|
|get_actions|function|获取自定义的actions|


### formatter 方法
+ 入参

|字段|类型|说明|
|---|---|---|
|obj|object|当前对象|
|field_name|str|字段名|
|value|object|字段值|

+ 返回

返回参数为单元格显示的值，可以是html，可以是文本。

### formatter例子
```python
def formatter(self, obj, field_name, value):
    # 这里可以对value的值进行判断，比如日期格式化等
    return value
```

### delete_queryset 说明

例子：

```python
def delete_queryset(self, request, queryset):
    #这里可以做些自定义的处理
    pass
```
+ 返回

如果返回的是queryset，那么程序将会调用delete方法执行删除。如果返回的是None，将不会做任何动作



## get_queryset 说明

例子：

```python
 def get_queryset(self, request):
        qs = super().get_queryset(request)

        return qs.filter(id__gte=1)
```

注意，get_queryset可以返回为None，包括pass也是返回为None。如果为None，的时候 该queryset将会被丢弃，框架将会从model中获取queryset
```python
qs=model.objects.get_querset()
```

可以利用这个方法来控制自定义的数据权限，该方法与原生admin一致。

## get_list_display 说明

```python
    def get_list_display(self, request):
        # 这里可以进行判断，动态返回某些字段或表头
        return self.list_display
```
## get_list_filter 说明

```python
    def get_list_filter(self, request):
        # 这里可以进行判断，动态返回list_filter
        return self.list_filter
```
## get_actions 说明

```python
    def get_actions(self, request):
        # 这里可以进行判断，动态返回actions
        actions = super(EmployeAdmin, self).get_actions(request)
    return actions
```

## Media 自定义引入js和css

Media 遵从原生django admin的写法，并且处理也是django实现，simplepro未做任何文件处理，只做引入动作。

例子：

```python

@admin.register(Department)
class DepartmentAdmin(admin.ModelAdmin):

    class Media:
        js=('aa.js','bb.js')
        css=('dd.css','cc.css')

```

在页面解析的时候 会调用下列方式进行引入
```html
<script type="text/javascript" src="{% static 'aa.js'%}">
```

核心源码：

引入js部分：

```html
 {% if media.js %}
    {% for js in media.js %}
    <script type="text/javascript" src="{% static js %}"></script>
    {% endfor %}
{% endif %}
```

引入css部分：

```html
{% if media.css %}
    {% for css in media.css %}
    <link rel="stylesheet" href="{% static css %}">
    {% endfor %}
{% endif %}
```

在通过引入自己的js和css之后，可以对页面进行扩展，调用simplepro的js sdk 可以实现意想不到的效果，比如表格format，添加自定义按钮，拦截按钮事件等。具体请看jssdk
