# simplepro 自定义按钮
自定义按钮也叫action，是ModelAdmin中的actions一员。
定义例子：
```python

    actions=['custom_button']
    def custom_button(self, request, queryset):
        pass

```

该按钮文档与simpleui完全一致，请查看文档[自定义按钮](https://github.com/newpanjing/simpleui/blob/master/QUICK.md#%E8%87%AA%E5%AE%9A%E4%B9%89%E6%8C%89%E9%92%AE)

simplepro在自定义actions的基础上进行了扩展，入参和出参与django admin不同。

+ 入参

入参是只request.POST中收到的参数

|字段|类型|说明|
|-----|-----|-----|
|action|string|该值是用于simplepro路由使用，可以不予理会|
|all|string|取值0和1，0未全部选择，1全部选中|
|key|string|自定义按钮的方法名|
|ids|string|逗号分隔的选中的id，如果all=1，该值没有|

+ 出参

出参是指自定义按钮方法体返回的数据

例子：
```python
def test(self, request, queryset):
    return {
        'state': False,
        'msg': '用户关联的数据还没有删除！'
    }
```

+ 字段说明：

|字段|类型|说明|
|-----|-----|-----|
|state|boolean|true成功，false失败|
|msg|string|前台通知框的信息|
