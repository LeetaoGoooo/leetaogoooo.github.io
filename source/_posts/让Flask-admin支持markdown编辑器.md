---
title: 让Flask-admin支持markdown编辑器
tags: [Python,Flask]

date: 2018-11-30
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

flask-admin 算是一个很不错的 flask 后台管理了,用它来做博客系统的管理后端再合适不过了,节约时间成本,避免重复造轮子,但是作为一个程序员,写文章怎么可以没有 markdown 呢? 现在让我们尝试一下让 flask-admin 支持 markdown 吧.

# 实践

## Flask-PageDown

这个库是对 Pagedown 的封装,将其集成到了Flask-WTF表单中了。具体内容可以去它的 github 仓库看一下: [Flask-PageDown](https://github.com/miguelgrinberg/Flask-PageDown)

### 优缺点

**优点**:
    
1. 配置简单
2. 对 flask-admin 的改动较小

**缺点**:

1.  无法实现对代码的高亮
2.  生成的编辑器是上下布局的,不太美观

### 使用方法

#### 安装和配置

通过 pip 进行安装:

```
pip install flask-pagedown
```

然后在项目中初始化:

```python
from flask_pagedown import PageDown
app = Flask(__name__)
pagedown = PageDown(app)
```

当然也可以通过 init_app(app) 的方式进行初始化

```python
from flask import Flask
from flask_pagedown import PageDown

pagedown = PageDown()

def create_app(config_name):
    app = Flask(__name__, static_folder='static', static_url_path='')
    app.config.from_object(config[config_name])
    # 省略部分代码
    pagedown.init_app(app)
    return app
```

这个编辑器还依赖两个 js 文件,所以还需要在我们的对应的模板中添加进去相应的方法:

```html
<html>
    <head>
    {{ pagedown.html_head() }}
    </head>
    <body>
    ...
    </body>
</html>
```

默认情况下这两个 js 是通过 CDN 加载的,也就意味着你的服务器需要可以访问网络,如果只想通过本地就可以访问的话,很简单把这两个 js 文件下载下来,然后将 pagedown.html_head() 替换成两个文件的本地路径就可以了. 两个文件的下载地址: [Markdown.Converter](https://cdnjs.cloudflare.com/ajax/libs/pagedown/1.0/Markdown.Converter.min.js),
[Markdown.Sanitizer](https://cdnjs.cloudflare.com/ajax/libs/pagedown/1.0/Markdown.Sanitizer.min.js)

#### 新增自定义的 ModelView

flask-admin 支持自定义的 ModelView, 这是我们可以实现这个功能的基础.

```python
class PeachPostView(ModelView):

    form_overrides = {
        'content': PageDownField
    }

    def __init__(self, model, session, **kwargs):
        super(PeachPostView, self).__init__(model, session, **kwargs)

    # 省略部分代码
```

最后在我们的 flask-admin 给我们需要 markdown 编辑器的 model 添加上自定义的 ModelView 就可以了.

```python
# 省略部分代码
admin.add_view(PeachPostView(Post, db.session, endpoint='AdminPost'))
```

完整代码可以查看 [use:使用 flask-pagedown 作为 markdown 编辑器](https://github.com/lt94/peach-blog/tree/cbf375ee764aa5bf4797f526ab8ef7357ba44aa0)

最终的效果图:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fxq60xchfvj31cm0nhgmy.jpg)

## 使用开源的 markdown 编辑器

没错,这个方法就比较自由了,找自己喜欢的 markdown 编辑器然后集成进去. 这里我最终选择了 [editor.md]
(https://github.com/pandao/editor.md.git) 这个开源编辑器,这个好像很久没维护了,但是颜值挺高的了,就决定是它了.

### 优缺点

**优点**:

1.  优点很明星,颜值高,满足我对 markdown 的所有需求了,支持代码高亮.

**缺点**:

1.  对 flask-admin 的改动较多,需要自己写的部分比较多.

### 使用方法

#### 下载 editor.md 插件

插件下载很简单,从官网上或者 github 上下载, 下载完成后,我这里将 editor.md 下载的文件保存到 static 的 plugin 的目录下了.

#### 覆盖 flask-admin 原有文件

这里由于我们需要使用插件,所以我们需要对 flask-admin 原有的 create.html 和 edit.html 文件进行重写.

1. 在 templates 目录下新建 admin 目录
2. 在 admin 目录下创建 model 目录, 并新增 peach-post-create.html, peach-post-edit.html 文件,将 flask-admin 对应的 create.html 和 edit.html 内容复制进去.
3. 在 admin 目录下新增 peach-base.html 和 peach-lib.html 文件,将 flask-admin 对应的 base.html 和 lib.html 文件复制进去

tip: 上述说所的 flask-admin 的内容,均在你的开发环境中找到 flask-admin 的库所在目录,然后需要复制的文件在: flask_admin\templates\bootstrap3\admin 下

然后配置,使其访问我们指定的文件

```python
# 自定义的 modelview
class PeachPostView(ModelView):
    # 指定访问文件
    create_template = 'admin/model/peach-post-create.html'
    edit_template = 'admin/model/peach-post-edit.html'

    def __init__(self, model, session, **kwargs):
        super(PeachPostView, self).__init__(model, session, **kwargs)

# 指定 base_template 文件
admin = Admin(name=name, template_mode=template_mode,index_view=PeachAdminIndexView(), base_template='admin/peach-base.html')
# 省略部分代码
admin.add_view(PeachPostView(Post, db.session, endpoint='AdminPost'))

```

#### 修改文件, 支持 markdown 

动手修改文件的之前,我们需要先了解一下 flask-amdmin 原有的文件是什么样子的,然后才能正确的修改,这里以 create.html 为例子,截取关键代码.

```html
# create.html
{% import 'admin/lib.html' as lib with context %}
# ...
{% block edit_form %}
    {{ lib.render_form(form, return_url, extra(), form_opts) }} # 使用了 lib 中的函数,看一下这个函数
{% endblock %}

# lib.html

{% macro render_form(form, cancel_url, extra=None, form_opts=None, action=None, is_modal=False) -%}
    {% call form_tag(action=action) %}
        {{ render_form_fields(form, form_opts=form_opts) }}  # 重点是表单渲染,再看一下 render_form_fields 函数
        {{ render_form_buttons(cancel_url, extra, is_modal) }}
    {% endcall %}
{% endmacro %}

{% macro render_form_fields(form, form_opts=None) %}
    # 省略部分代码
          {{ render_field(form, f, kwargs) }}  # 看一下 render_field 这个函数
{% endmacro %}

{% macro render_field(form, field, kwargs={}, caller=None) %}
  {% set direct_error = h.is_field_error(field.errors) %}
  <div class="form-group{{ ' has-error' if direct_error else '' }}">
    <label for="{{ field.id }}" class="col-md-2 control-label">{{ field.label.text }}
        {% if h.is_required_form_field(field) %}
          <strong style="color: red">&#42;</strong>
        {%- else -%}
          &nbsp;
        {%- endif %}
    </label>
    <div class="{{ kwargs.get('column_class', 'col-md-10') }}">
      {% set _dummy = kwargs.setdefault('class', 'form-control') %}
      {{ field(**kwargs)|safe }}
      {% if field.description %}
      <p class="help-block">{{ field.description|safe }}</p>
      {% endif %}
      {% if direct_error %}
        <ul class="help-block input-errors">
        {% for e in field.errors if e is string %}
          <li>{{ e }}</li>
        {% endfor %}
        </ul>
      {% endif %}
    </div>
    {% if caller %}
      {{ caller(form, field, direct_error, kwargs) }}
    {% endif %}
  </div>
{% endmacro %}
```

通过上述 flask-admin 源码的浏览,我们不难发现,最终的渲染最核心的函数就是 lib.html 中的 render_field 函数, 要达到我们的目的,我们就需要对这个函数进行一点的修改了.由于我们只需要对数据库模型的某个字段支持 markdown 就可以了,所以需要我们可以通过 field.id == '字段名' 的方法去判断,从而让他支持 markdown.

为了更好的理解接下来的代码,我们对 editor.md 的使用做一个简单的说明:

```html
<link rel="stylesheet" href="editormd.min.css" />
<div id="editormd">
    <textarea style="display:none;">### Hello Editor.md !</textarea>
</div>
```

```js
<script src="jquery.min.js"></script>
<script src="editormd.min.js"></script>
<script type="text/javascript">
    $(function() {
        var editor = editormd("editormd", {
            path : "../lib/" // Autoload modules mode, codemirror, marked... dependents libs path
        });

        /*
        // or
        var editor = editormd({
            id   : "editormd",
            path : "../lib/"
        });
        */
    });
</script>
```

说明完了怎么使用 editor.md,我们来说一下我们最终的思路, 通过字段名去判断,然后符合添加的情况下,新建一个 div, id 为 editor,然后在 peach-post-create.html 按照 editor.md 的使用方法渲染.说完了思路,接下来就看代码.

**修改 peach-lib.html 和 peach-post-create.html 文件**

```html
# peach-lib.html
{% macro render_field(form, field, kwargs={}, caller=None) %}
  {% set direct_error = h.is_field_error(field.errors) %}
  <div class="form-group{{ ' has-error' if direct_error else '' }}">
    <label for="{{ field.id }}" class="col-md-2 control-label">{{ field.label.text }}
        {% if h.is_required_form_field(field) %}
          <strong style="color: red">&#42;</strong>
        {%- else -%}
          &nbsp;
        {%- endif %}
    </label>
    <div class="{{ kwargs.get('column_class', 'col-md-10') }}">
      {% set _dummy = kwargs.setdefault('class', 'form-control') %}
      {% if field.id == 'content' %} # 符合条件新建一个 div
        <div id='editormd'>
            {{ field(**kwargs)|safe }}
        </div>
      {% else %}
        {{ field(**kwargs)|safe }}
      {% endif %}
      {% if field.description %}
      <p class="help-block">{{ field.description|safe }}</p>
      {% endif %}
      {% if direct_error %}
        <ul class="help-block input-errors">
        {% for e in field.errors if e is string %}
          <li>{{ e }}</li>
        {% endfor %}
        </ul>
      {% endif %}
    </div>
    {% if caller %}
      {{ caller(form, field, direct_error, kwargs) }}
    {% endif %}
  </div>
{% endmacro %}

# peach-post-create.html
{% extends 'admin/master.html' %}
{% import 'admin/peach-lib.html' as lib with context %}
{% from 'admin/peach-lib.html' import extra with context %} {# backward compatible #}

# 省略部分代码

  {{ super() }}
  {{ lib.form_css() }}
  <link href="{{ url_for('static', filename='plugins/editor.md/css/editormd.min.css')}}" rel='stylesheet'>
{% endblock %}

# 省略部分代码

{% block tail %}
  {{ super() }}
  {{ lib.form_js() }}
  <script src="{{ url_for('static', filename='plugins/editor.md/editormd.js')}}"></script>
  <script type="text/javascript">
    $(function() {
        var editor = editormd("editormd", {
            path : "/plugins/editor.md/lib/",
            height  : 640
        });
    });
  </script>
{% endblock %}
```

**修改 modelview**

```python
class PeachPostView(ModelView):
    # 省略部分代码
    create_template = 'admin/model/peach-post-create.html'

admin = Admin(name=name, template_mode=template_mode,index_view=PeachAdminIndexView(), base_template='admin/peach-base.html')
# 省略部分代码
admin.add_view(PeachPostView(Post, db.session, endpoint='AdminPost'))
```

完整代码可以查看 [add:使用 editor.md 编辑器](https://github.com/lt94/peach-blog/tree/12a2d6e5fd1f6b5fefb235c496e7816a123c88ee)


最后看一下效果图:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fxpv6inei2j31lu17pn4w.jpg)


# 最后

到此大功告成,欢迎大家关注我的公共号-Leetao, 偶尔分享 flask 之外的知识.