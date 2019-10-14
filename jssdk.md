# JS-SDK

为了提供更好的前端扩展，simplepro提供了jssdk，在不用重新页面的同时，进行页面最高程度的扩展。

## 使用例子：


首先通过Admin中的Media引入自定义的js文件

例子：
```python

@admin.register(Department)
class DepartmentAdmin(admin.ModelAdmin):

    class Media:
        js=('aa.js','bb.js')
        css=('dd.css','cc.css')
```

然后aa.js中 注册事件

```javascript

//所有的方法或者属性都是可选

SIMPLEAPI={
    //在vue完成初始化后调用
    init:function(app){
        //通过app对象，可以控制页面上所有的元素显示隐藏

    },
    toolbar:function(type,app){
        //type=按钮类型，添加=add，删除=delete,编辑=edit,其他自定义按钮的type就是自身的方法名
        //app=当前vue对象，可以通过console.log(app)查看当前所有的变量

        //this域
        //如果是add,edit,delete this域 是空的
        //如果是自定义按钮，this域是自定义按钮的配置信息

        //返回true，代表后续步骤还是由simplepro处理
        return true
    },
    loadData:function(app){
        //加载数据的时候触发
        //改变app某些参数的时候 建议在此方法进行，init中改后，重新加载数据将会对某些值进行覆盖
    }
    
    //其他例如formatter这一类，在ModelAdmin中已经提供
}

```