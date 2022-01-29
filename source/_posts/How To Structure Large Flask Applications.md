---
title: How To Structure Large Flask Applications
tags: [Python,Flask]

date: 2017-06-02
published: true
hideInList: false
feature: 
isTop: false
---








>整理翻译自[How To Structure Large Flask Applications](https://www.digitalocean.com/community/tutorials/how-to-structure-large-flask-applications)

# 介绍

其实已经有许多关于如何组织架构 Python Web 应用的方法和约定。尽管现在有些框架本身承载了一些工具(脚手架)从而可以达到实现自动化和降低任务难度的效果,但是究根结底几乎所有的解决方案都是依赖于打包/模块化应用作为代码块从而达到分发到相关的文件和文件夹的目的。

最小的 web 应用开发框架 Flask 有着它特有的(工具)-blueprints。

在这篇数码海洋的文章里,我们将会了解到如何使用 Flask 的 blueprints 来创建一个应用目录和组织架构它从而使其变成可重用的组件。这些将会给你的维护和组件的开发带来巨大的好处和便捷。

## 学术表

1. Flask：最小的应用开发框架
2. 关于我们在这篇文章的选择
3. 准备Flask系统
    1. 准备操作系统
    2. 安装Python,pip以及virtualenv
4. 组织架构应用的目录
    1. 创建应用目录
    2. 创建虚拟环境
    3. 创建应用文件
    4. 安装 Flask
5. 使用模块和 Blueprints(组件)
    1. 模块基础
    2. 模块模板
6. 创建应用(run.py,init.py 等等)
    1. 使用 nano 编辑 run.py
    2. 使用 nano 编辑 config.py
7. 创建一个模块/组件
    1. 组织架构模型
    2. 定义模块数据类型
    3. 定义模块表单
    4. 定义应用控制器(views)
    5. 在 "app/init.py" 中建立应用
    6. 创建模板
    7. 实际操作中查看你的模型


## Flask:最小的应用开发框架

Flask 迷你的框架但是丝毫不会影响重要事情的处理方式。相反, Flask 允许开发者使用他们渴望的并且熟悉的工具。出于这个目的,它由此产生了自己的扩展索引和大量已经存在并解决大多数事情的的工具从登陆到日志方方面面。

它不是一个严格的“常规”框架，部分依赖于配置文件,这些配置文件使得其在开始使用和保持事项时变得更容易。

## 我们在文章中的选择

通过我们前面浏览的内容了解到,Flask 的方式就是让我们使用那些用起来最舒服的工具。在我们的文章中,我们将会使用最常见的扩展和库。这些选择包括:

1. SQLAlchemy
2. WTForms

### Flask-SQLAlchemy

在 Flask 中添加 SQLAlchemy 支持很快捷。这是一个很好的扩展。

```shell
Author: Armin Ronacher
PyPI Page: Flask-SQLAlchemy
Documentation: Read docs @ packages.python.org
On Github: [mitsuhiko/flask-sqlalchemy](https://github.com/mitsuhiko/flask-sqlalchemy)
```

### Flask-WTF

Flask-WTF 提供简单的 WTForms 集成。集成包括可选择的 用来提供更好安全保障的 CSRF。这也是一个很好的扩展。

```shell
Author: Anthony Ford (created by Dan Jacob)
PyPI Page: Flask-WTF
Documentation: Read docs @ packages.python.org
On Github: [ajford/flask-wtf](https://github.com/mitsuhiko/flask-wtf)
```

## 准备 Flask 系统

### 准备操作系统

使用如下命令安装必要的开发工具

```
aptitude update
aptitude -y upgrade
aptitude install -y build-essential python-dev python2.7-dev
```

### 安装python,pip以及virtualenv

参考 [Flask入门——从无到有构建一个网站 （二）环境搭建](https://zhuanlan.zhihu.com/p/26663186)

## 组织应用的目录

我们将使用典型的大型应用名称作为我们的应用的文件夹。在文件夹里，我们将会有一个虚拟环境与此同时还有应用的包以及一些其他的文件夹包括 用来运行 测试(开发)环境的 "run.py" 和 Flask 的配置文件 "config.py"

下面给出的作为样例的目录结构，该目录结构是高度可扩展的,它构建的目的是充分利用 Flask 和其他库提供的工具。不要被它吓着了，我们将会一步一步构建它直至完成。

目标样例结构:

```shell
~/LargeApp
    |-- run.py
    |-- config.py
    |__ /env             # Virtual Environment
    |__ /app             # Our Application Module
         |-- __init__.py
         |-- /module_one
             |-- __init__.py
             |-- controllers.py
             |-- models.py                
         |__ /templates
             |__ /module_one
                 |-- hello.html
         |__ /static
         |__ ..
         |__ .
    |__ ..
    |__ .
```

### 创建应用文件夹

让我们开始创建我们需要的主要目录。
逐条去执行下述命令:

```shell
mkdir ~/LargeApp
mkdir ~/LargeApp/app
mkdir ~/LargeApp/app/templates
mkdir ~/LargeApp/app/static   
```

执行完成之后我们的当前目录

```shell
~/LargeApp
    |__ /app             # Our Application Module
         |__ /templates
         |__ /static
```
### 创建一个虚拟环境


使用一个虚拟环境能给你带来很多好处。对于你的每一个应用都强烈推荐使用一个新的虚拟环境。在你的应用中创建一个虚拟环境是一个很好的让你应用有序和干净的方法。

使用下列命令创建一个虚拟环境

```shell
cd ~/LargetApp
virtualenv env
```

### 创建应用文件

在这步，我们将会在使用 modules 和 blueprints 之前创建我们基础应用文件。

运行下列文件创建基本应用文件:

```shell
touch ~/LargeApp/run.py
touch ~/LargeApp/config.py
touch ~/LargeApp/app/__init__.py
```

我们当前的结构:

```shell
~/LargeApp
    |-- run.py
    |-- config.py
    |__ /env             # Virtual Environment
    |__ /app             # Our Application Module
         |-- __init__.py
         |__ /templates
         |__ /static
```

### 安装 Flask 和相关依赖库

目录什么都创建好之后，我们开始使用 pip 下载安装我们的Flask

使用下述命令将 Flask 安装到虚拟环境当中:

```shell
cd ~/LargeApp
env/bin/pip install flask
env/bin/pip install flask-sqlalchemy
env/bin/pip install flask-wtf
```

## 使用 Modules 和 Blueprints（组件）

### 基础模块

在走到这一步的时候，我们已经将我们的应用结构创建好了并且也将所有的依赖下载完成了。

我们的目标是模块化可逻辑分组的所有相关模块。

举个简单的例子比如认证系统。将所有视图，控制器，模型和帮助器置于一个位置，以允许可重用性的方式进行设置，这使得这种结构化成为维护应用程序同时提高生产力的好方法。


目标案例模块(组件)结构(在/app里面)：

```shell
# Our module example here is called *mod_auth*
# You can name them as you like as long as conventions are followed

/mod_auth
    |-- __init__.py
    |-- controllers.py
    |-- models.py
    |-- ..
    |-- .
```

### 模块模板

为了使得达到最大的模块化，我们将遵循上述规定创建 “templates” 文件夹,同时并保护一个与模块相同或相似的相关名称的新文件夹，以包含其模板文件。

目标样例模板目录结构(在 LargeApp 中):

```shell
/templates
    |-- 404.html
    |__ /auth
         |-- signin.html
         |-- signup.html
         |-- forgot.html
         |-- ..
         |-- .
```

## 创建应用

在本节中，我们将继续前面的步骤，并从我们的应用程序的实际编码开始，然后再转到创建第一个模块化组件（使用蓝图）：mod_auth，其用来处理所有与身份验证相关的过程。

### 使用 nano 编辑 "run.py"

```shell
nano ~/LargeApp/run.py
```

输入如下内容:

```shell
# Run a test server.
from app import app
app.run(host='0.0.0.0', port=8080, debug=True)
```

保存退出。

### 使用 nano 编辑 "config,py"

```shell
nano ~/LargeApp/config.py
```

输入如下内容:

```shell
# Statement for enabling the development environment
DEBUG = True

# Define the application directory
import os
BASE_DIR = os.path.abspath(os.path.dirname(__file__))  

# Define the database - we are working with
# SQLite for this example
SQLALCHEMY_DATABASE_URI = 'sqlite:///' + os.path.join(BASE_DIR, 'app.db')
DATABASE_CONNECT_OPTIONS = {}

# Application threads. A common general assumption is
# using 2 per available processor cores - to handle
# incoming requests using one and performing background
# operations using the other.
THREADS_PER_PAGE = 2

# Enable protection agains *Cross-site Request Forgery (CSRF)*
CSRF_ENABLED     = True

# Use a secure, unique and absolutely secret key for
# signing the data. 
CSRF_SESSION_KEY = "secret"

# Secret key for signing cookies
SECRET_KEY = "secret"
```

保存退出。

## 创建模块/组件

本节是定义本文核心的第一个主要步骤。 在这里，我们将看到如何使用Flask的蓝图来创建一个模块（即组件）。

### 第一步 结构化模块

正如我们已经开始做的，让我们创建我们的第一个模块（mod_auth）目录和文件来开始工作。

```shell
# Create the module directory inside the *app* module
mkdir ~/LargeApp/app/mod_auth

# Create where module's templates will reside
mkdir ~/LargeApp/app/templates/auth

# Create __init__.py to set the directory as a Python module
touch ~/LargeApp/app/mod_auth/__init__.py

# Create module's controllers and models etc.
touch ~/LargeApp/app/mod_auth/controllers.py
touch ~/LargeApp/app/mod_auth/models.py
touch ~/LargeApp/app/mod_auth/forms.py

# Create module's templates
touch ~/LargeApp/app/templates/auth/signin.html

# Create a HTTP 404 Error page
touch ~/LargeApp/app/templates/404.html
```

在上述操作过程之后,我们的目录结构类似这样:

```shell
~/LargeApp
    |-- run.py
    |-- config.py
    |__ /env             # Virtual Environment
    |__ /app             # Our Application Module
         |-- __init__.py
         |-- /mod_auth   # Our first module, mod_auth
             |-- __init__.py
             |-- controllers.py
             |-- models.py
             |-- forms.py
         |__ /templates
             |-- 404.html
             |__ /auth
                 |-- signin.html
         |__ /static
```

### 第二步 定义模块数据模型

```shell
nano ~/LargeApp/app/mod_auth/models.py
```

输入下述内容

```python
# Import the database object (db) from the main application module
# We will define this inside /app/__init__.py in the next sections.
from app import db

# Define a base model for other database tables to inherit
class Base(db.Model):

    __abstract__  = True

    id            = db.Column(db.Integer, primary_key=True)
    date_created  = db.Column(db.DateTime,  default=db.func.current_timestamp())
    date_modified = db.Column(db.DateTime,  default=db.func.current_timestamp(),
                                           onupdate=db.func.current_timestamp())

# Define a User model
class User(Base):

    __tablename__ = 'auth_user'

    # User Name
    name    = db.Column(db.String(128),  nullable=False)

    # Identification Data: email & password
    email    = db.Column(db.String(128),  nullable=False,
                                            unique=True)
    password = db.Column(db.String(192),  nullable=False)

    # Authorisation Data: role & status
    role     = db.Column(db.SmallInteger, nullable=False)
    status   = db.Column(db.SmallInteger, nullable=False)

    # New instance instantiation procedure
    def __init__(self, name, email, password):

        self.name     = name
        self.email    = email
        self.password = password

    def __repr__(self):
        return '<User %r>' % (self.name)      
```

保存并退出

### 第三步 定义模块表单

```shell
nano ~/LargeApp/app/mod_auth/forms.py
```

输入下述内容:

```python
# Import Form and RecaptchaField (optional)
from flask.ext.wtf import Form # , RecaptchaField

# Import Form elements such as TextField and BooleanField (optional)
from wtforms import TextField, PasswordField # BooleanField

# Import Form validators
from wtforms.validators import Required, Email, EqualTo


# Define the login form (WTForms)

class LoginForm(Form):
    email    = TextField('Email Address', [Email(),
                Required(message='Forgot your email address?')])
    password = PasswordField('Password', [
                Required(message='Must provide a password. ;-)')])
```

保存并退出

### 第四步 定义应用控制器(视图)

```shell
nano ~/LargeApp/app/mod_auth/controllers.py
```

输入下述内容

```python
# Import flask dependencies
from flask import Blueprint, request, render_template, \
                  flash, g, session, redirect, url_for

# Import password / encryption helper tools
from werkzeug import check_password_hash, generate_password_hash

# Import the database object from the main app module
from app import db

# Import module forms
from app.mod_auth.forms import LoginForm

# Import module models (i.e. User)
from app.mod_auth.models import User

# Define the blueprint: 'auth', set its url prefix: app.url/auth
mod_auth = Blueprint('auth', __name__, url_prefix='/auth')

# Set the route and accepted methods
@mod_auth.route('/signin/', methods=['GET', 'POST'])
def signin():

    # If sign in form is submitted
    form = LoginForm(request.form)

    # Verify the sign in form
    if form.validate_on_submit():

        user = User.query.filter_by(email=form.email.data).first()

        if user and check_password_hash(user.password, form.password.data):

            session['user_id'] = user.id

            flash('Welcome %s' % user.name)

            return redirect(url_for('auth.home'))

        flash('Wrong email or password', 'error-message')

    return render_template("auth/signin.html", form=form)
```

保存并退出

### 第五步 在应用中创建 "app/init.py"

```shell
nano ~/LargeApp/app/__init__.py
```

输入以下内容:

```python
# Import flask and template operators
from flask import Flask, render_template

# Import SQLAlchemy
from flask.ext.sqlalchemy import SQLAlchemy

# Define the WSGI application object
app = Flask(__name__)

# Configurations
app.config.from_object('config')

# Define the database object which is imported
# by modules and controllers
db = SQLAlchemy(app)

# Sample HTTP error handling
@app.errorhandler(404)
def not_found(error):
    return render_template('404.html'), 404

# Import a module / component using its blueprint handler variable (mod_auth)
from app.mod_auth.controllers import mod_auth as auth_module

# Register blueprint(s)
app.register_blueprint(auth_module)
# app.register_blueprint(xyz_module)
# ..

# Build the database:
# This will create the database file using SQLAlchemy
db.create_all()
```

保存并退出

### 第六步 创建模板

```shell
nano ~/LargeApp/app/templates/auth/signin.html
```

输入以下内容:

```html
{% macro render_field(field, placeholder=None) %}
{% if field.errors %}
<div>
{% elif field.flags.error %}
<div>
{% else %}
<div>
{% endif %}
    {% set css_class = 'form-control ' + kwargs.pop('class', '') %}
    {{ field(class=css_class, placeholder=placeholder, **kwargs) }}
</div>
{% endmacro %}

<div>
  <div>
    <legend>Sign in</legend>
    {% with errors = get_flashed_messages(category_filter=["error"]) %}
    {% if errors %}
    <div>
    {% for error in errors %}
    {{ error }}<br>
    {% endfor %}
    </div>
    {% endif %}
    {% endwith %}

    {% if form.errors %}
    <div>
    {% for field, error in form.errors.items() %}
    {% for e in error %}
    {{ e }}<br>
    {% endfor %}
    {% endfor %}
    </div>
    {% endif %}
    <form method="POST" action="." accept-charset="UTF-8" role="form">
      {{ form.csrf_token }}
      {{ render_field(form.email, placeholder="Your Email Address",
                                  autofocus="") }}
      {{ render_field(form.password, placeholder="Password") }}
      <div>
      <label>
        <input type="checkbox" name="remember" value="1"> Remember Me
      </label>
      <a role="button" href="">Forgot your password?</a><span class="clearfix"></span>
      </div>
      <button type="submit" name="submit">Sign in</button>
    </form>  
  </div>
</div>
```

保存并退出

### 第七步 从网页浏览你定义的模块

模块创建成功了，接下来就是查看我们定义的模板了

运行 `run.py` 文件启动开发环境服务器

```shell
cd ~/LargeApp
env/bin/python run.py
```

上述命令将会初始化一个开发环境服务器，端口为 8080.
通过 URL 去访问我们的模块

```shell
http://[your droplet's IP]/auth/signin
```
虽然您无法登录，但您可以通过输入一些示例数据或通过测试其验证器来查看它。

