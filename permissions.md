# simplepro 自定义权限

+ 在simpleui中没有提供自定义权限的支持。
+ 在原生django admin中没有提供自定义菜单权限的支持，自定义按钮权限支持。

# 权限基础知识

simplepro的权限 也是在django admin的权限中进行扩展的。但是未更改django 权限相关的任何字段。

## 表结构

|表名|名字|作用|
|---|---|---|
|django_content_type|模块类型表|用于记录用户和admin的各个模块|
|auth_permission|权限表|用户记录所有模块的view,add,change,delete权限，与django_content_type关联|
|auth_group|权限组|每个用户都可以指定一个或多个权限组，与auth_group_permissions关联|

以上三个表就是权限的基础表结构，通过表格或者`model`的`permission`字段所添加的权限，都会往`auth_permission`表中存入数据。



# 自定义按钮权限

自定义权限包括自定义按钮和自定义菜单，django admin中 对自定义的action 也就是自定义按钮提供了权限支持，定义如下：


## 注意：下面这种方式在simplepro中和原生admin中通用，simpleui中不适用。

## 1.定义

```python

# admin
@admin.register(Employe)
class EmployeAdmin(admin.ModelAdmin):
    #....定义显示字段之类

    actions=[test]

    # 自定义按钮
    def test(self, request, queryset):
        # 这里可有做一些crud
        pass

    test.short_descript='按钮显示的名'    

# model
class Employe(models.Model):
    name = models.CharField(max_length=128, verbose_name='名称', help_text='员工的名字', null=False, blank=False,
                            db_index=True)

    gender_choices = (
        (0, '未知'),
        (1, '男'),
        (2, '女'),
    )

    gender = models.IntegerField(choices=gender_choices, verbose_name='性别', default=0)

    class Meta:
        verbose_name = "员工"
        verbose_name_plural = "员工管理"

        # 定义自定义的权限
        permissions = (
            ('test', 'test111'),
            ('test2', 'test222'),
            ('权限显示的名称','权限判断的codename'),
            ('测试按钮','test') # 注意 这里的test要与admin中的按钮方法名一致
        )`

```

## 2.迁移

+ 生成迁移文件

```shell
python3 manage.py makemigrations
```
+ 执行迁移，添加到数据库

```shell
 python3 manage.py migrate
```

## 3.勾选权限
在操作完以上两个步骤后，我们可以进入到`认证和授权->组`或者`认证和授权->权限`中，对用户勾选该项权限。

## 4.其他操作

在`认证和授权->权限`该项菜单中，可以直接用表格形式添加。可以不用写在model中，但是这种方式容易遗漏，不推荐使用。


> 以上的步骤是自定义按钮的权限控制，下面的步骤中定义自定义菜单的权限稍微复杂一些。

# 自定义菜单权限

自定义菜单的权限定义方式有2种
1. 第一种和自定义按钮一样，定义在model中，但是前提是自定义按钮的`codename`字段要与model的`codename`一致，比较麻烦。

2. 第二种，直接在`认证和授权->权限`该项菜单中，用表格的形式直接添加。但是菜单的codename要与`内容类型`的codename一致。

## 如何查看app_label?
可以打开表：`django_content_type`进行查看。

自定义菜单权限：

例子1，在model中的方式：

model:

```python

class Employe(models.Model):
    name = models.CharField(max_length=128, verbose_name='名称', help_text='员工的名字', null=False, blank=False,
                            db_index=True)

    gender_choices = (
        (0, '未知'),
        (1, '男'),
        (2, '女'),
    )

    gender = models.IntegerField(choices=gender_choices, verbose_name='性别', default=0)

    class Meta:
        verbose_name = "员工"
        verbose_name_plural = "员工管理"

        # 定义自定义的权限
        permissions = (
            ('test', 'test111'),
            ('test2', 'test222'),
            ('权限显示的名称','权限判断的code'),
            ('测试按钮','test'), # 注意 这里的test要与admin中的按钮方法名一致
            ('Simpleui','view_simpleui')
        )`

```
> 添加了之后别忘记执行迁移


settings.py中的菜单定义：
```python

SIMPLEUI_CONFIG = {
    # 在自定义菜单的基础上保留系统模块
    'system_keep': True,
    'menus': [{
        'name': 'Simpleui',
        'icon': 'fas fa-code',
        'url': 'https://gitee.com/tompeppa/simpleui',
        'codename':'simpleui'
    }, {
        'name': '测试',
        'icon': 'fa fa-file',
        'codename': 'test',
        'models': [{
            'name': 'Baidu',
            'url': 'http://baidu.com',
            'icon': 'far fa-surprise',
            'codename':'baidu'
        }, {
            'name': '内网穿透',
            'url': 'https://www.wezoz.com',
            'icon': 'fab fa-github',
            'codename':'nat'
        }, {
            'name': '内网穿透',
            'url': 'https://www.wezoz.com',
            'icon': 'fab fa-github'
        }, {
            'name': '登录页嵌套测试',
            'url': '/login'
        }]
    }]
}

```

以上定义了两种菜单，一个是只有一级，一个是有2级

+ 一级的 权限key=codename.codename也就是 simpleui.simpleui
+ 二级的 权限key=test.baidu test.nat

主要保证这些数据在auth_permission中，权限就可以正常进行，否则将不受权限控制.

如果父级没有codename 及时子级写了codename 也不权限控制

自定义菜单如果是最顶级，需要在`内容类型->内容类型`菜单中添加一条数据。

## 不生效排查

1. 请保证 `request.user.get_all_permissions()` 该方法中有自定义的权限
2. 顶级菜单的权限是否存在，例如test.test 如果该项权限没有，子级也不会显示


# 导入导出权限

导入 key=model.import

导出 key=model.export


# FAQ常见问题

+ PermissionError: [Errno 13] Permission denied

请请检查是否对python lib目录有访问权限，linux和Windows默认用普通用户，对全局python lib包没有写的权限，所有会提示权限问题。建议用虚拟环境运行。

+ 无法获取套餐列表
 
 请检查是否能访问simpleui.88cto.com
 
 ```shell
ping simpleui.88cto.com
 ```

 如果被防火墙屏蔽，请采用hosts的方式，在hosts文件中加入：
 ```
103.39.210.34 simpleui.88cto.com
 ```