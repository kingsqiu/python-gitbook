# url调度

## **django如何处理请求**

当一个用户请求Django 站点的一个页面，下面是Django 系统决定执行哪个Python 代码遵循的算法：

1. Django 决定要使用的根URLconf 模块。 通常，这是[`ROOT_URLCONF`](https://yiyibooks.cn/__trs__/xx/Django_1.11.6/ref/settings.html#std:setting-ROOT_URLCONF)设置的值，但是如果传入的`HttpRequest`对象具有[`urlconf`](https://yiyibooks.cn/__trs__/xx/Django_1.11.6/ref/request-response.html#django.http.HttpRequest.urlconf)属性（由中间件设置），则其值将被用于代替[`ROOT_URLCONF`](https://yiyibooks.cn/__trs__/xx/Django_1.11.6/ref/settings.html#std:setting-ROOT_URLCONF)设置。

2. Django 加载该Python 模块并寻找可用的`urlpatterns`。 这应该是一个 [`django.urls.path()`](https://docs.djangoproject.com/zh-hans/2.2/ref/urls/#django.urls.path) 实例或者一个  [`django.urls.re_path()`](https://docs.djangoproject.com/zh-hans/2.2/ref/urls/#django.urls.re_path) 实例

3. Django 依次匹配每个URL 模式，在与请求的URL 匹配的第一个模式停下来。

4. 一旦正则表达式匹配，Django将导入并调用给定的视图，该视图是一个简单的Python函数（或基于类的[class-based view](https://yiyibooks.cn/__trs__/xx/Django_1.11.6/topics/class-based-views/index.html)）。

    

   视图将获得如下参数:

   -  一个 [`HttpRequest`](https://docs.djangoproject.com/zh-hans/2.1/ref/request-response/#django.http.HttpRequest) 实例。 
   - 如果匹配的正则表达式返回了没有命名的组，那么正则表达式匹配的内容将作为位置参数提供给视图。
   - 关键字参数由正则表达式匹配的命名组组成，但是可以被 [`django.urls.path()`](https://docs.djangoproject.com/zh-hans/2.1/ref/urls/#django.urls.path) 或[`django.urls.re_path()`](https://docs.djangoproject.com/zh-hans/2.1/ref/urls/#django.urls.re_path). 的可选参数`kwargs`覆盖。

5. 如果没有匹配到正则表达式，或者如果过程中抛出一个异常，Django 将调用一个适当的错误处理视图。 请参见官网的 [错误处理](https://docs.djangoproject.com/zh-hans/2.1/topics/http/urls/#error-handling)  。

例如：

```python
from django.urls import path

from . import views

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>/', views.month_archive),
    path('articles/<int:year>/<int:month>/<slug:slug>/', views.article_detail),
]
```

注意：

- 要从URL捕获值，使用尖括号
- 捕获到的值可以选择一个类型约束。例如，使用 `<int:name>`去捕获一个整数形参，如果没有设定类型约束，任何的字符串，除了一个"/"字符，都会被捕获。
- 不需要添加起始"/"， 因为每个URL都有该斜杠 

## **Path converters**

默认情况，以下Path converters可用：

-  `str`- 匹配任何非空 字符串，但路径分隔符除外`'/'`。如果表达式中不包含转换器，则为默认设置。 

-  `int`-匹配零或任何正整数。返回一个int。 

-  `slug`-匹配由ASCII字母或数字以及连字符和下划线字符组成的任何条形字符串。例如， `building-your-1st-django-site`。 

-  `uuid`-匹配格式化的UUID。为防止多个URL映射到同一页面，必须包含破折号并且字母必须小写。例如，`075194d3-6885-417e-a8a8-6c931e272f00`。返回一个 [`UUID`](https://docs.python.org/3/library/uuid.html#uuid.UUID)实例。 

- `path`-匹配任何非空字符串，包括路径分隔符 `'/'`。这样，您就可以与完整的URL路径进行匹配，而不仅仅是与URL路径的一部分进行匹配`str`。 

### **注册自定义Path converters**

看文档吧

## **使用正则表达式**

如果 URLconf 和 converters 语法不能满足你定义URL路径的需求，可以使用正则表达式，为此，要使用 [`re_path()`](https://docs.djangoproject.com/zh-hans/2.1/ref/urls/#django.urls.re_path) 替换 [`path()`](https://docs.djangoproject.com/zh-hans/2.1/ref/urls/#django.urls.path). 

在Python正则表达式中，命名正则表达式组的语法为`(?P<name>pattern)`，其中name是组的名称，并且 pattern是匹配的模式。

上面的例子用正则重写后：

```python
from django.urls import path, re_path

from . import views

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/(?P<slug>[\w-]+)/$', views.article_detail),
]
```

此时：

-  将要匹配的确切URL受到更多限制。 
-  无论正则表达式进行哪种匹配，每个捕获的参数都将作为字符串发送到视图。 

当从使用 [`path()`](https://docs.djangoproject.com/zh-hans/2.1/ref/urls/#django.urls.path) 切换到  [`re_path()`](https://docs.djangoproject.com/zh-hans/2.1/ref/urls/#django.urls.re_path) 时，要特别注意视图参数的类型或许会产生变动，可能需要调整视图，反之亦然。

**也可以不使用命名**，但是会有更多错误产生。

## **嵌套参数**

## **指定视图参数的预设值**

## **错误处理**

## **传递额外的选项来查看函数**

URLconfs 具有一个钩子，让你传递一个Python 字典作为额外的参数传递给视图函数。

[`django.conf.urls.url()`](https://yiyibooks.cn/__trs__/xx/Django_1.11.6/ref/urls.html#django.conf.urls.url) 函数可以接收一个可选的第三个参数，它是一个字典，表示想要传递给视图函数的额外关键字参数。

**传递额外的参数给include**

直接将额外的参数传递给include就可以给include包含的每个应用都传递该参数

## **url命名**

 为了完成URL反查，你将需要使用**命名的URL模式**。 URL名称使用的字符串可以包含任何你喜欢的字符。 没有限制你只使用合法的Python名称。 

### **url的命名空间**

- 反查命名空间的URL
- URL命名空间和包含的URLconf

### **url反向解析**

在 Django 项目中经常需要获取最终形式的 URL，这么做是为了在生成的内容中嵌入 URL（视图和素材资源网址，呈现给用户的网址，等等）， 或者用于在服务器端处理导航流程（重定向等）

此时，一定不能硬编码 URL（费时、不可伸缩，而且容易出错）， 或者参照 URL 配置创造一种生成 URL 的机制，因为这样非常容易导致线上 URL 失效。

换句话讲，我们需要的是一个 DRY 机制。 这种机制的一个优点是，当改进 URL 设计之后无需在项目源码中大范围搜索、替换失效的 URL。

我们可以获得URL的主要信息是负责处理URL的视图的标识（例如名称）。 必须参与正确URL查找的其他信息片段是视图参数的类型（位置，关键字）和值。

Django 提供了一种方案，只需在 URL 映射中设计 URL。 我们为其提供 URL 配置，然后就可以双向使用：

- 根据用户/浏览器发起的URL 请求，它调用正确的Django 视图，并从URL 中提取它的参数需要的值。
- 根据Django 视图的标识和将要传递给它的参数的值，获取与之关联的URL。

第一种方式是我们在前面的章节中一直讨论的用法。 第二种方式叫做*反向解析URL*、*反向URL匹配*、*反向URL查询*或者简单的*URL反查*。

在需要URL 的地方，对于不同层级，Django 提供不同的工具用于URL 反查：

- 在模板中：使用 [`url`](https://docs.djangoproject.com/zh-hans/2.2/ref/templates/builtins/#std:templatetag-url)  模板标签。
- 在Python代码中：使用 [`reverse()`](https://docs.djangoproject.com/zh-hans/2.2/ref/urlresolvers/#django.urls.reverse)  函数。
- 在更高层的与处理Django 模型实例相关的代码中：使用  [`get_absolute_url()`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/instances/#django.db.models.Model.get_absolute_url)  方法。

示例：

```python
from django.conf.urls import url

from . import views

urlpatterns = [
    #...
    url(r'^articles/([0-9]{4})/$', views.year_archive, name='news-year-archive'),
    #...
]
# name确定url的命名，在模板和python代码中使用。
```

python中使用`reverse()`方法，传入命名的字符串作为参数

```python
from django.urls import reverse
from django.http import HttpResponseRedirect

def redirect_to_year(request):
    # ...
    year = 2006
    # ...
    return HttpResponseRedirect(reverse('news-year-archive', args=(year,)))
```

模板中使用`{% url url字符串 %}`：

```html
<a href="{% url 'news-year-archive' 2012 %}">2012 Archive</a>
{# Or with the year in a template context variable: #}
<ul>
{% for yearvar in year_list %}
<li><a href="{% url 'news-year-archive' yearvar %}">{{ yearvar }} Archive</a></li>
{% endfor %}
</ul>
```

