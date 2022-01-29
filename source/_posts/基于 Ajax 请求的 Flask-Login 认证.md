---
title: 基于 Ajax 请求的 Flask-Login 认证
tags: [Flask,Flask Web 开发,Python]

date: 2019-01-03
published: true
hideInList: false
feature: 
isTop: false
---



index.html
example.py
```

## index.html

```index.html
<!DOCTYPE html>
<html>
    <head>
        <title> test login by Leetao</title>
    </head>
    <body>
        <p id="test_login"></p>
        <button onclick="load_msg()">未点击</button>
        <script src="//cdnjs.cloudflare.com/ajax/libs/jquery/1.12.4/jquery.min.js"></script>
        <script>
            var load_msg = function () {
                $.get('/hello?api_key=test_login',function(data){
                    $('#test_login')[0].innerText = data
                })
            }
        </script>
    </body>
</html>
```

## example.py

```python
from flask import Flask, request, jsonify, render_template
from flask_login import LoginManager, current_user, login_required
login_manager = LoginManager()

app = Flask(__name__)
login_manager.init_app(app)

class User:
    
    def __init__(self,user_name):
        self.id = 'test_id'
        self.user_name = user_name

    @property
    def is_active(self):
        return True

    @property
    def is_authenticated(self):
        return True

    @property
    def is_anonymous(self):
        return False

    def get_id(self):
        try:
            return text_type(self.id)
        except AttributeError:
            raise NotImplementedError('No `id` attribute - override `get_id`')

user = User("leetao")

@login_manager.request_loader
def load_from_request(request):
    api_key = request.args.get('api_key')
    if api_key == 'test_login':
        return user
    return None

@app.route('/hello')
@login_required
def hello_world():
    print(current_user.user_name)
    return jsonify('Hello, World!')

@app.route("/")
def index():
    return render_template("index.html")
```

## 结果

为了方便理解,我截了两张图,一张是 api_key 正确的情况下,一张是 错误的情况下

### api_key 正确

![](http://ww1.sinaimg.cn/large/006wYWbGly1fytiw435blj30zh0g740b.jpg)

### api_key 错误

![](http://ww1.sinaimg.cn/large/006wYWbGly1fytiwcjbcej30vk0i1abi.jpg)