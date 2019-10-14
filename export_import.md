# simplepro 导入导出

simplepro导入导出是依赖 `django-import-export`插件，配置请参考该插件的文档。

在自定义按钮附近使用导出和右边工具栏使用导入导出：


```python

from import_export import resources
from import_export.admin import ImportExportModelAdmin, ImportExportActionModelAdmin

class ProxyResource(resources.ModelResource):
    class Meta:
        model = Employe

@admin.register(Employe)
class EmployeAdmin(ImportExportActionModelAdmin):
    resource_class = ProxyResource

```

