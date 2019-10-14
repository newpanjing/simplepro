# simplepro 安装指南

simplepro 依赖 simpleui、django-export-import、以及django


1. 安装simplepro

```shell
pip install simplepro
```

2. INSTALL_APPS中配置

> 按以下顺序加入到INSTALL_APPS数组的顶部
```python
INSTALLED_APPS = [
    'simplepro',
    'simpleui',
    'import_export',
    ......
]    
```
3. 配置中间件

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    # 加入simplepro的中间件
    'simplepro.middlewares.SimpleMiddleware'
]
```

+ 如果在开启DEBUG模式后，启动即可运行。
+ 如果关闭后，需要克隆静态文件，步骤与simpleui一致。[文档地址](https://github.com/newpanjing/simpleui/blob/master/QUICK.md#%E5%85%8B%E9%9A%86%E9%9D%99%E6%80%81%E6%96%87%E4%BB%B6%E5%88%B0%E6%A0%B9%E7%9B%AE%E5%BD%95)
