# API

This document describes the  API for django-echarts.

## Views

Views is the core component for rendering charts.

### EChartsMixin

`django_echarts.views.base.EChartsMixin`

A Mixin for build a echarts instance.

**get_echarts_instance**

`get_echarts_instance(self, *args, **kwargs)`

The interface,every subclass must implement this interface and return a echarts instance.

### EChartsFrontendView

`django_echarts.views.frontend.EChartsFrontendView`

The view class will use frontend way (ajax) to render charts.

### EChartsBackendView

`django_echarts.views.backend.EChartsBackendView`

The view class will use backendway (template) to render charts.

## App Settings

The default value are listed as the following code fragment.

```python
{
    'echarts_version':'3.7.0',
    'lib_js_host':'bootcdn',
    'map_js_host':'echarts',
    'local_host':None
}
```

### echarts_version

The version string of echarts which you are using. e.g `3.7.0`.It is used for the most CDN hosts.

### lib_js_host

The repository which project provides **Echarts libary javascript file**.The following values are available:

- A CDN name: valid choices are `cdnjs` / `npmcdn` /` bootcdn` / `pyecharts` / `echarts`.
- A format string representing the host url,which supports the following CASE-SENSITIVE fields.
    - STATIC_URL: the value of `settings.STATIC_URL`. If you do not provide the value of `settings.STATIC_URL` ,it will not pass to context.
    - echarts_version: the version of echarts.

This table list the CDN names what *lib_js_host* supports.

| name      | host url format                          |
| --------- | ---------------------------------------- |
| cdnjs     | https://cdnjs.cloudflare.com/ajax/libs/echarts/{echarts_version} |
| npmcdn    | [https://unpkg.com/echarts@{echarts_version}/dist](https://unpkg.com/echarts@{echarts_version}/dist) |
| bootcdn   | [https://cdn.bootcss.com/echarts/{echarts_version}](https://cdn.bootcss.com/echarts/{echarts_version}) |
| pyecharts | [https://chfw.github.io/jupyter-echarts/echarts](https://chfw.github.io/jupyter-echarts/echarts) |
| echarts   | http://echarts.baidu.com/dist            |

### map_js_host

The repository which project provides **Echarts map javascript file**.The table lists the CDN names what the settings item supports.

| name      | host url format                          |
| --------- | ---------------------------------------- |
| echarts   | http://echarts.baidu.com/asset/map/js    |
| pyecharts | [https://chfw.github.io/jupyter-echarts/echarts](https://chfw.github.io/jupyter-echarts/echarts) |

>  Note:The *echarts* host supports only http schema,so it will goes wrong when you deploy with HTTPS using this host in some brower(e.g Google Chrome).  

### local_host

The repository which provides javascript files.Note that the string value MUST start with the value of `settings.STATIC_URL`.

## Project Settings Access

### DJANGO_ECHARTS_SETTINGS

`django_echarts.utils.DJANGO_ECHARTS_SETTINGS`

> New in v0.1.3

In your code, you should use the module variable `django_echarts.utils.DJANGO_ECHARTS_SETTINGS` to access the project's settings and their related attributes.It is a instance of `django_echarts.utils.SettingsStore`.

You can also access using `DJANGO_ECHARTS_SETTINGS['foo']` or `DJANGO_ECHARTS_SETTINGS.foo`.

### DJANGO_ECHARTS_SETTING

> Deprecated

The alias for `DJANGO_ECHARTS_SETTINGS`, and will be removed in v0.2 . 

### SettingsStore

`django_echarts.utils.SettingsStore(**kwargs)`

A public settings class for access in the project.

## Template Tags

These tags are define at *echarts* module, you should load it in you template code before using.

```
{% laod echarts %}
```

### echarts_options

`django_echarts.templatetags.echarts.echarts_options(echarts)`

Render javascript template for a Echarts objects.

### echarts_container

`django_echarts.templatetags.echarts.echarts_container(echarts_instance)`

Render the DOM element (e.g `<div></div>`) where the chart will show on.

### echarts_js_dependencies

`django_echarts.templatetags.echarts.echarts_js_dependencies(*args)`

Render javascript script nodes for echarts,custom name.It is a enhance version of `echarts_js`.

### echarts_js_content

`django_echarts.templates.echarts.echarts_js_content(*echarts_list)`

Render javascript node for initial code, multiple echarts will use the same `script` html node.

## Plugins

*django-echarts* provides some plugins to enhance features.

### Host

`django_echarts.plugins.staticfiles.HostStore(name_or_host, context=None, host_lookup=None)`

A class representing a local or remote repository.

### HostStore

`django_echarts.plugins.staticfiles.HostStore(context=None, echarts_lib_name_or_host=None, echarts_map_name_or_host=None, **kwargs)`

A manager for echarts library and map javascript files.

## Data Builder Tools

These method will be useful when you add data to `Base` instance from other formats. 

### Cast

`pyecharts.base.Base.cast(seq)`

A static method to Convert the sequence with the dictionary and tuple type into k_lst, v_lst.

### Pluck

`pluck.pluck(iterable, *keys, **kwargs)`

Pick fields from a iterable.

## Tool Commands

These command can be used from *manage.py* script,and they alse support [default options](https://docs.djangoproject.com/en/1.11/ref/django-admin/#default-options).

```
python manage.py COMMAND Foo1 Foo2
```

### download_echarts_js

```
usage: manage.py download_echarts_js [-h] [--version] [-v {0,1,2,3}]
                                     [--settings SETTINGS]
                                     [--pythonpath PYTHONPATH] [--traceback]
                                     [--no-color] [--js_host JS_HOST]
                                     js_name [js_name ...]
```

Download remote file to the local file system.You must specify a directory value to settings.STATIC_URL.

The remote repository name will be found by this order:

- the `js_host` optional param.
- the `settings.DJANGO_ECHARTS['lib_js_host']` or  `settings.DJANGO_ECHARTS['map_js_host']` 