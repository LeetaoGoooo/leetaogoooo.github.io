---
title: Flask-Login用户会话管理
tags: [Python,Flask,源码]

date: 2017-10-28
published: true
hideInList: false
feature: 
isTop: false
---







想必大家对 Flask-Login 这个 Flask 扩展肯定不会陌生,毕竟作为一个应用,用户登录之后,他们的认证状态是需要被记录下来的,浏览其他的页面也是需要使用这个状态的,但是这一过程是怎么发挥作用的呢?让我们从源码的层次上简单认识一下.

# Flask-Login 使用的准备
使用 flask-login 的之前,我们必须要实现下述四个方法:
![](http://ww1.sinaimg.cn/large/006wYWbGly1fkur7vprn0j30n804kmxd.jpg)
但是为什么要实现这个方法呢?别着急我们在下面会释疑.
除此之外我们还需要写下述的这样的代码:

```python
@login_manager.user_loader
def load_user(username):
    if query_user(username) is not None:
        curr_user = User()
        curr_user.id = username
        return curr_user
```
上面的为用户的回调函数接收字符串表示的唯一用户标识符,如果能找到该用户,则返回该用户对象否则返回None.但是<code>user_loader</code>究竟是什么?
源码:

```python
class LoginManager(object):
    ...
    def user_loader(self, callback):
        self.user_callback = callback
        return callback
```
似乎没什么作用呢?别着急,接着往下看,我们通过具体使用去理解源码

# login_user()
但凡时候 Flask-Login 应该都用过 <code>login_user()</code>,所以让我们看看使用这个函数发生了什么.

```python
def login_user(user, remember=False, force=False, fresh=True):
    if not force and not user.is_active: # 最初必须实现的四个方法之一,在这里发挥作用,如果该用户不允许登录则直接返回False(黑名单etc)
        return False

    user_id = user.get_id() # 修改
    session['user_id'] = user_id
    session['_fresh'] = fresh
    session['_id'] = current_app.login_manager._session_identifier_generator() # 根据客户端的User Agent和IP生成标识,strong模式下一旦用户标识检测失败,便会清空所有的session内容

    if remember:
        session['remember'] = 'set'

    _request_ctx_stack.top.user = user
    user_logged_in.send(current_app._get_current_object(), user=_get_user())
    return True
```
主要内容是将用户信息放入session中,并为改session生成标识符.

# @login_required
我们通过 @login_required 去限制用户页面访问,这一过程是怎么实现的?

```python
def login_required(func):
    @wraps(func)
    def decorated_view(*args, **kwargs):
        if request.method in EXEMPT_METHODS: // EXEMPT_METHODS = set(['OPTIONS']) config.py 第 49 行,通常该请求是获取服务器支持的HTTP请求方法
            return func(*args, **kwargs)
        elif current_app.login_manager._login_disabled: # 如果 _login_disabled 被设置为 True,则装饰器将会被忽略,通常用在单元测试可以很方便的关闭认证
            return func(*args, **kwargs)
        elif not current_user.is_authenticated: # 判断用户是否登录
            return current_app.login_manager.unauthorized() # 没有登录的情况下:1.如果注册了 LoginManager.unauthorized_handler 则这个时候调用这个函数 2. 向用户提示 LoginManager.login_message信息 3.有 login_view的情况下,跳转到login_view,没有则返回abort(401)
        return func(*args, **kwargs)
    return decorated_view
```
# unauthorized()
在上述<code>@login.required</code>中我们说明了,<code>unauthorized()</code>在需要登录的情况下采取的行动,现在简单的看一下代码:

```python
    def unauthorized(self):
        user_unauthorized.send(current_app._get_current_object())

        if self.unauthorized_callback:
            return self.unauthorized_callback()

        if request.blueprint in self.blueprint_login_views:
            login_view = self.blueprint_login_views[request.blueprint]
        else:
            login_view = self.login_view

        if not login_view:
            abort(401)

        if self.login_message:
            if self.localize_callback is not None:
                flash(self.localize_callback(self.login_message),
                      category=self.login_message_category)
            else:
                flash(self.login_message, category=self.login_message_category)

        config = current_app.config
        if config.get('USE_SESSION_FOR_NEXT', USE_SESSION_FOR_NEXT):
            login_url = expand_login_view(login_view)
            session['next'] = make_next_param(login_url, request.url)
            redirect_url = make_login_url(login_view)
        else:
            redirect_url = make_login_url(login_view, next_url=request.url)

        return redirect(redirect_url)
```
# current_user
在登录之后,我们在需要使用当前登录对象的时候,都会使用 <code>current_user</code>,那么它是怎么发挥作用的?

```python
current_user = LocalProxy(lambda: _get_user()) // 当前用户的代理,这里重点关注 _get_user()

# _get_user()
# 调用 login_user() 之后显然 _request_ctx_stack.top 中存在 user,则直接返回 
def _get_user():
    if has_request_context() and not hasattr(_request_ctx_stack.top, 'user'):
        current_app.login_manager._load_user()

    return getattr(_request_ctx_stack.top, 'user', None)

# 那么如果不存在,会发生什么? 调用 _loder_user()

# _loader_user()
    def _load_user(self):
        user_accessed.send(current_app._get_current_object())

        config = current_app.config
        if config.get('SESSION_PROTECTION', self.session_protection): // session 保护,可以取值为 None(禁用),basic(在 basic 模式下或会话是永久的，如果该标识未匹配，会话会简单地被标记为非活 跃的，且任何需要活跃登入的东西会强制用户重新验证，前提你已经使用了活跃登入机制),strong(在 strong 模式下的非永久会话，如果该标识未匹配，整个会话或者记住的令牌如果存在将会被删除)
            deleted = self._session_protection()
            if deleted:
                return self.reload_user()

        # 如果 记住你的 cookie 存在,而 session 不存在,则会将 cookie 中的 user_id 赋值给 session
        # 但是如果这个时候处于登出的状态的话,会发生什么呢?
        is_missing_user_id = 'user_id' not in session
        if is_missing_user_id:
            cookie_name = config.get('REMEMBER_COOKIE_NAME', COOKIE_NAME)
            header_name = config.get('AUTH_HEADER_NAME', AUTH_HEADER_NAME)
            has_cookie = (cookie_name in request.cookies and
                          session.get('remember') != 'clear') // 登出的时候 has_cookie = False
            if has_cookie:
                return self._load_from_cookie(request.cookies[cookie_name])
            elif self.request_callback: //定义在 request_loader() 中, 正常情况下我们并不使用,仅当我们不想使用 cookie 的情况下登录用户才会考虑 request_loader 回调.
                return self._load_from_request(request)
            elif header_name in request.headers: 
                return self._load_from_header(request.headers[header_name]) # 该函数调用链中的 header_loader() 已经被弃用

        return self.reload_user()
```

# logout_user()
登录要验证账户密码什么的,显的比较复杂,但是登出就很简答了,你不需要传递任何参数,只需调用这个函数,那让我们看看这个函数发生了什么:
```python
def logout_user():
    user = _get_user() # 从_request_ctx_stack.top.user,session 或者 remember_me cookie 中获取

    if 'user_id' in session:
        session.pop('user_id')

    if '_fresh' in session:
        session.pop('_fresh')

    cookie_name = current_app.config.get('REMEMBER_COOKIE_NAME', COOKIE_NAME)
    if cookie_name in request.cookies:
        session['remember'] = 'clear'

    user_logged_out.send(current_app._get_current_object(), user=user)

    current_app.login_manager.reload_user() 
    return True
```
该函数将session中的user相关信息全部清空,然后重新加载用户,正常逻辑下由于重新加载用户后如果当前页面需要登录才能访问的话,则会跳转到登录视图.

# fresh_login_required
当用户登入，他们的会话被标记成“新鲜的”，就是说在这个会话只中用户实际上登录过。当会话销毁用户使用“记住我”的 cookie 重新登入，会话被标记成“非新鲜的”。fresh_login_required 除了验证用户登录，也将确保他们的登录是“新鲜的”。如果不是“新鲜的”，它会把用户送到可以重输入验证条件的页面,主要用户修改个人信息的敏感操作. 

```python
def fresh_login_required(func):
    @wraps(func)
    def decorated_view(*args, **kwargs):
        if request.method in EXEMPT_METHODS:
            return func(*args, **kwargs)
        elif current_app.login_manager._login_disabled:
            return func(*args, **kwargs)
        elif not current_user.is_authenticated:
            return current_app.login_manager.unauthorized()
        elif not login_fresh(): // 获取session 中的 refresh 值
            return current_app.login_manager.needs_refresh() // 1.出现 LoginMange.needs_refress_message 2.跳转到 LoginManger.refresh_view,如果没有设置则会提示 401 错误
        return func(*args, **kwargs)
    return decorated_view
```

# confirm_login
将会话重新标记为"新鲜"

```python
def confirm_login():
    session['_fresh'] = True
    session['_id'] = current_app.login_manager._session_identifier_generator() // 重新生成会话id
    user_login_confirmed.send(current_app._get_current_object())
```


# reload_user()
接着 <code>logout_user()</code> 中调用 <code>reload_user()</code>.很显然这个函数的功能就是重新加载用户,具体实现让我们看下源码

```python
# logout_user() 中调用, user 使用默认参数为 None
    def reload_user(self, user=None):
        ctx = _request_ctx_stack.top // _request_ctx_stack 一个保存对象的栈,可以返回看 login_user() 源码的倒数第三行,登录的时候将 user 保存在 _request_ctx_stack.top.user 中

        if user is None: 
            user_id = session.get('user_id') // logout_user() 中 session 已经将 user_id 移除
            if user_id is None:
                ctx.user = self.anonymous_user() //见下文
            else:
                if self.user_callback is None: //这里解释了为什么我们需要定义 @login_manager.user_loader
                    raise Exception(
                        "No user_loader has been installed for this "
                        "LoginManager. Add one with the "
                        "'LoginManager.user_loader' decorator.")
                user = self.user_callback(user_id) // 这里我们最初定义的user_loader发挥作用,通过调用self.user_callback()获取用户
                if user is None:
                    ctx.user = self.anonymous_user()
                else:
                    ctx.user = user
        else:
            ctx.user = user
```

# anoymous_user
源码如下:

```python
self.anonymous_user = AnonymousUserMixin // 匿名类

# AnonymousUserMixin
class AnonymousUserMixin(object):
    @property
    def is_authenticated(self):
        return False

    @property
    def is_active(self):
        return False

    @property
    def is_anonymous(self):
        return True

    def get_id(self):
        return
```




