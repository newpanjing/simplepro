# simplepro 图表说明

图表类型与配置完全遵循echarts4.x 具体效果请看echarts的文档，simplepro只是做了个基于dom的封装，原生的写法在vue中无法渲染，会出现页面空白。

simpleui的图表采用echarts4.x，echarts的具体配置请移步至[https://www.echartsjs.com/zh/index.html](https://www.echartsjs.com/zh/index.html)查看

## 如何配置
由于simpleui和simplepro是基于vue开发，所以echarts在simplepro中进行了封装。直接在home页面引用echarts既可。
```html

<echarts :option="" style="width:100px;height:200px;"></echarts>

```
### 参数说明：
|参数|说明|
|---|---|
|option|option字段完成与echarts的option一致，封装的过程中没有做任何处理。|
|style|和原生dom的style一致，没有做任何处理，但是必须指定height，否则将不能正常显示图表|

### 例子

```html
<script type="text/javascript">
    var option1 = {
    title: {
        text: ''
    },
    tooltip : {
        trigger: 'axis',
        axisPointer: {
            type: 'cross',
            label: {
                backgroundColor: '#6a7985'
            }
        }
    },
    legend: {
        data:['邮件营销','联盟广告','视频广告','直接访问','搜索引擎']
    },
    toolbox: {
        feature: {
            saveAsImage: {}
        }
    },
    grid: {
        left: '3%',
        right: '4%',
        bottom: '3%',
        containLabel: true
    },
    xAxis : [
        {
            type : 'category',
            boundaryGap : false,
            data : ['周一','周二','周三','周四','周五','周六','周日']
        }
    ],
    yAxis : [
        {
            type : 'value'
        }
    ],
    series : [
        {
            name:'邮件营销',
            type:'line',
            stack: '总量',
            areaStyle: {},
            data:[120, 132, 101, 134, 90, 230, 210]
        },
        {
            name:'联盟广告',
            type:'line',
            stack: '总量',
            areaStyle: {},
            data:[220, 182, 191, 234, 290, 330, 310]
        },
        {
            name:'视频广告',
            type:'line',
            stack: '总量',
            areaStyle: {},
            data:[150, 232, 201, 154, 190, 330, 410]
        },
        {
            name:'直接访问',
            type:'line',
            stack: '总量',
            areaStyle: {normal: {}},
            data:[320, 332, 301, 334, 390, 330, 320]
        },
        {
            name:'搜索引擎',
            type:'line',
            stack: '总量',
            label: {
                normal: {
                    show: true,
                    position: 'top'
                }
            },
            areaStyle: {normal: {}},
            data:[820, 932, 901, 934, 1290, 1330, 1320]
        }
    ]
};

</script>
<echarts :option="option1" style="width: 100%;height: 300px"></echarts>

```

以上方式是静态渲染，需要在页面输出数据。但是这样往往无法满足需求。所以动态渲染也是同样支持的。

### 动态渲染
如果想动态改变数据，可以把option交由最外层的vue实例管理，把本页定义的option1先删除

然后定义在app中：
```js
app.option1={};
ajax(function(data){
    app.option1=data;
});
```

### 注意
注意，如果在子页面实现，需要自行引入echarts的js文件。然后在引入

例如：
```html
<script type="text/javascript" src="{% static '/admin/simpleui-x/js/vue.min.js' %}"></script>
<script type="text/javascript" src="{% static 'admin/simplepro/echarts/echarts.min.js' %}"></script>
<script type="text/javascript" src="{% static '/admin/simplepro/js/chart.js' %}"></script>
```
以上三个文件缺一不可。