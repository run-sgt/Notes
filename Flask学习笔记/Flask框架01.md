# Flask框架01

## 1.基础铺垫

### 1-1  装饰器知识扩展

```python
# 回顾正常的装饰器样式
def wrapper(func):
    def inner(*args, **kwargs):
        return func(*args, **kwargs)
    return inner
@wrapper
def index(request):
    pass
```

```python
# 带参装饰器
def wrapper(option):
    def inner(func, *args, **kwargs):
        return func(*args, **kwargs)
    return inner
@wrapper({'k1': 'v1'})
def index(request):
    pass
```

```markdown
# 分析
当装饰器带参数时
首先：wrapper({'k1': 'v1'})会开始执行 -->返回inner
然后：@wrapper(..) ==> @inner
我们知道装饰器在装饰函数时候会将函数名当作参数传进装饰器,并执行，所以：
@inner --> inner(index)

让我们总结一下：
带参装饰器，只要当前py文件一执行，
就会立即执行wrapper({'k1': 'v1'})
然后立即执行inner(index)
```

```python
# 变形
url_map = {
    '/index': index
}
def route(option):
    def inner(func, *args, **kwargs):
        url_map[option['path']] = func
    return inner
@route({'path': '/index'})
def index(request):
    pass
```

---

### 1-2 session & cookie 原理(自行回顾)

### 1-3 类的继承 一个知识点

```python
class MyDict(dict):
	def __init__(self, *args, **kwargs):
        super(MyDict, self).__init__(*args, **kwargs)
        self.modify = True
obj = MyDict()
那么obj就会自带modify=True的属性
意思就是比如：字典可以被继承，然后我们在这个类上面在开发我们需要的功能     
```

## 2.三大框架的介绍

```
Django：大而全的web框架，内部包含非常多的组件：ORM、Form、ModelForm、缓存、Session、中间件、信号等
```

```
Flask：短小精悍，内部没有太多组件。第三方组件非常丰富，可定制性非常强。类似简单的任务应用，会比较简洁。路由比较特殊，它是基于装饰器来实现，但是究其本质其实就是通过内置的add_url_rule方法来实现。
```

```
Tornado：异步非阻塞框架(node.js)
```

## 3.Flask框架基础

### 3-1 安装与本质

首先说说flask

```
Flask是一个基于python开发并且依赖jinjia2模版与Werzeug WSGI服务的一个微型框架，而Werkzeug的本质就是socket服务端，用于接收http请求并对请求进行预处理，然后出发Flask框架，开发人员基于Flask框架提供的功能对请求进行处理，然后将渲染后的记过返回给浏览器
```

a.安装

```
pip3 install flask
```

b.`wsgi，werkzeug完成socket功能`

**`werkzeug`示例：**

```python
from werkzeug.wrappers import Request, Reponse
@Request.application
def hell(request):
    return Reponse('Hello World')
if __name__ == '__main__':
    from werkzeug.serving import runsimple
    runsimple('localhost', 4000, hello)  # 监听端口，请求一来就执行hello函数    
```

**`wsgiref`示例：**

```python
from wsgiref.simple_server import make_server
def runserver(environ, start_response):
	start_response('200 OK', [('Content-Type', 'text/html')])
    return [bytes('<h1>Hello Web !</h1>', encoding='utf-8'), ]
if __name__ == '__main__':
    httpd = make_server('', 8000, runserver)
    httpd.server_forever()
```

本质的本质就是`socket`

![1566131969347](../md_img/1566131969347.png)

### 3-2 使用

#### 3-2-1 基本使用

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World'

if __name__ == '__main__':
    app.run()

```

#### 3-2-2 配置文件

```
flask中的配置文件是一个flask.config.Config对象（继承字典）,默认配置为：
    {
        'DEBUG':                                get_debug_flag(default=False),  是否开启Debug模式
        'TESTING':                              False,                          是否开启测试模式
        'PROPAGATE_EXCEPTIONS':                 None,                          
        'PRESERVE_CONTEXT_ON_EXCEPTION':        None,
        'SECRET_KEY':                           None,
        'PERMANENT_SESSION_LIFETIME':           timedelta(days=31),
        'USE_X_SENDFILE':                       False,
        'LOGGER_NAME':                          None,
        'LOGGER_HANDLER_POLICY':               'always',
        'SERVER_NAME':                          None,
        'APPLICATION_ROOT':                     None,
        'SESSION_COOKIE_NAME':                  'session',
        'SESSION_COOKIE_DOMAIN':                None,
        'SESSION_COOKIE_PATH':                  None,
        'SESSION_COOKIE_HTTPONLY':              True,
        'SESSION_COOKIE_SECURE':                False,
        'SESSION_REFRESH_EACH_REQUEST':         True,
        'MAX_CONTENT_LENGTH':                   None,
        'SEND_FILE_MAX_AGE_DEFAULT':            timedelta(hours=12),
        'TRAP_BAD_REQUEST_ERRORS':              False,
        'TRAP_HTTP_EXCEPTIONS':                 False,
        'EXPLAIN_TEMPLATE_LOADING':             False,
        'PREFERRED_URL_SCHEME':                 'http',
        'JSON_AS_ASCII':                        True,
        'JSON_SORT_KEYS':                       True,
        'JSONIFY_PRETTYPRINT_REGULAR':          True,
        'JSONIFY_MIMETYPE':                     'application/json',
        'TEMPLATES_AUTO_RELOAD':                None,
    }
 
方式一：
    app.config['DEBUG'] = True
 
    PS： 由于Config对象本质上是字典，所以还可以使用app.config.update(...)
 
方式二：
    app.config.from_pyfile("python文件名称")
        如：
            settings.py
                DEBUG = True
 
            app.config.from_pyfile("settings.py")
 
    app.config.from_envvar("环境变量名称")
        环境变量的值为python文件名称名称，内部调用from_pyfile方法
 
 
    app.config.from_json("json文件名称")
        JSON文件名称，必须是json格式，因为内部会执行json.loads
 
    app.config.from_mapping({'DEBUG':True})
        字典格式
        
正式使用方式 
    app.config.from_object("python类或类的路径")
 
        app.config.from_object('pro_flask.settings.TestingConfig')
 
        settings.py
 
            class Config(object):
                DEBUG = False
                TESTING = False
                DATABASE_URI = 'sqlite://:memory:'
 
            class ProductionConfig(Config):
                DATABASE_URI = 'mysql://user@localhost/foo'
 
            class DevelopmentConfig(Config):
                DEBUG = True
 
            class TestingConfig(Config):
                TESTING = True
 
        PS: 从sys.path中已经存在路径开始写
     
 
    PS: settings.py文件默认路径要放在程序root_path目录，
    如果instance_relative_config为True，则就是instance_path目录(一般不会这样设置)
```

#### 3-2-3 路由系统

```
@app.route('/user/<username>')				# 传字符串
@app.route('/post/<int:post_id>')			# 传整数
@app.route('/post/<float:post_id>')			# 传小数
@app.route('/post/<path:path>')				# 传路径
@app.route('/login', methods=['GET', 'POST'])

给路由起别名,使用endpoint='***'
@app.route('/login', methods=['GET', 'POST'], endpoint='user_login')
(通过查看源码，如果endpoint不写，默认会指定为函数名)
反向解析时候：导入url_for
然后在使用的函数内：url_for是能传参数的，路由对应的函数要传参数后面也能传
				url = url_for('user_login', **values)
				return redirect(url)
```

常用路由系统有以上五种，所有的路由系统都是基于一下对应关系来处理：

```
DEFAULT_CONVERTERS = {
    'default':          UnicodeConverter,
    'string':           UnicodeConverter,
    'any':              AnyConverter,
    'path':             PathConverter,
    'int':              IntegerConverter,
    'float':            FloatConverter,
    'uuid':             UUIDConverter,
}
```

注册路由原理

```python
1.Flask路由实现：通过内置方法add_url_rule,实现路由
Flask的路由是通过装饰器来注册，本质上其实是通过内置的add_url_rule方法实现
比如Flask一个简单的路由是这样写的
@app.route('/login', methods=['GET', 'POST'], endpoint='my_login')
def login():
    ...
    return redirect('/index')
其实如果我们分析Flask源码路由注册部分大致能知道其实可以将上面的代码变成下面这样：
def login():
    ...
    return redirect('/index')
app.add_url_rule('/login', 'my_login', login, methods=['GET', 'POST'])
实现过程其实是一样的。
所以说Flask的路由是可以改成类似Django的路由样式的：
app.add_url_rule('/login', 'log', login, methods=['GET', 'POST'])
app.add_url_rule('/register', 'reg', register, methods=['GET', 'POST'])
app.add_url_rule('/home', 'home', home, methods=['GET', 'POST'])
app.add_url_rule('/detail', 'detail', home, methods=['GET', 'POST'])

2.Flask路由的CBV方式实现
class IndexView(views.MethodView):
    methods = ['GET']
    decorators = [auth, ]

    def get(self):
        return 'Index.GET'

    def post(self):
        return 'Index.POST'

app.add_url_rule('/index', view_func=IndexView.as_view(name='index'))  # name=endpoint

3.相关参数
@app.route和app.add_url_rule参数：
    rule,                       URL规则
    view_func,                  视图函数名称
    defaults=None,              默认值,当URL中无参数，函数需要参数时，使用defaults={'k':'v'}为函数提供参数
    endpoint=None,              名称，用于反向生成URL，即： url_for('名称')
    methods=None,               允许的请求方式，如：["GET","POST"]
    
    
    strict_slashes=None,        对URL最后的 / 符号是否严格要求，
                                如：
                                    @app.route('/index',strict_slashes=False)，
                                        访问 http://www.xx.com/index/ 或 http://www.xx.com/index均可
                                    @app.route('/index',strict_slashes=True)
                                        仅访问 http://www.xx.com/index 
    redirect_to=None,           重定向到指定地址
                                如：
                                    @app.route('/index/<int:nid>', redirect_to='/home/<nid>')
                                    或
                                    def func(adapter, nid):
                                        return "/home/888"
                                    @app.route('/index/<int:nid>', redirect_to=func)
                            
    subdomain=None,             子域名访问
    使用方法
    from flask import Flask, views, url_for

    app = Flask(import_name=__name__)
    app.config['SERVER_NAME'] = 'wupeiqi.com:5000'

    @app.route("/", subdomain="admin")
    def static_index():
        """Flask supports static subdomains
        This is available at static.your-domain.tld"""
        return "static.your-domain.tld 

    @app.route("/dynamic", subdomain="<username>")
    def username_index(username):
        """Dynamic subdomains are also supported
        Try going to user1.your-domain.tld/dynamic"""
        return username + ".your-domain.tld"
```

tips

```python
给视图函数加一个认证登录的装饰器：
def login_auth(func):
    def inner(*args, **kwargs):
        if not session.get('user_info'):
            url = url_for('login')
            return redirect(url)
        return func(*args, **kwargs)
    return inner
# 再回顾装饰器的2步：  
# 比如有个detail函数需要login认证登录于是：
@route('/detail', methods=['GET', 'POST'])
@login_auth
def detail():
    pass
# 分析：
# 装饰器是由顺序的，从上到下装饰，很显然login_auth装饰器应该写在路由装饰器的下面，不然路由都进不来，所以：
# route装饰其先将login_auth装饰与login_auth装饰了的detail
# 那么，我们知道@login_auth ==> inner(detail),那么route装饰的函数实际上可以说是一个inner函数，如果route装饰一个带装饰器的函数不会存在问题，但是如果装饰多个之后，就出问题了：
# 我们知道，route路由默认会将路由命名为它装饰的函数，除非指定名称，否则就是endpoint=装饰的函数，且不能重复，否则会出错
# 那么现在的问题出现了，如果存在多个认证登录装饰器装饰函数，最外层包的route装饰器肯定会有，那么如果不指定endpoint路由名称的话，那么这个endpoint都会默认=inner这个函数，所以会出现endpoint重复，故而报错：
# AssertionError: View function mapping is overwriting an existing endpoint function: inner
# 解决方法很简单：给每个路由指定一个名称endpoint='xx'
```

自定制正则路由匹配

```
from flask import Flask, views, url_for
from werkzeug.routing import BaseConverter

app = Flask(import_name=__name__)
class RegexConverter(BaseConverter):
    """
    自定义URL匹配正则表达式
    """
    def __init__(self, map, regex):
        super(RegexConverter, self).__init__(map)
        self.regex = regex
    # 真正匹配时候会执行to_python
    def to_python(self, value):
        """
        路由匹配时，匹配成功后传递给视图函数中参数的值
        :param value:
        :return:
        """
        return int(value)

    # 反向解析的时候会执行to_url
    def to_url(self, value):
        """
        使用url_for反向生成URL时，传递的参数经过该方法处理，返回的值用于生成URL中的参数
        :param value:
        :return:
        """
        val = super(RegexConverter, self).to_url(value)
        return val

# 添加到flask中
app.url_map.converters['regex'] = RegexConverter

@app.route('/index/<regex("\d+"):nid>')
def index(nid):
    # nid是个字符串
    print(nid)
    return 'Index'

if __name__ == '__main__':
    app.run()
```

#### 3-2-4 模版

1、模板的使用

Flask使用的是Jinja2模板，所以其语法和Django无差别

后端dict在前端可以使用句点符、[]、get（）三种方式取值

dict遍历需要加()

```
<tbody>
    {% for id,user in users.items() %}
    <tr>
        <td>{{ id }}</td>
        <td>{{ user.name }}</td>
        <td>{{ user['gender'] }}</td>
        <td>{{ user.get('age') }}</td>
        <td><a href="/detail/{{ id }}">用户信息</a></td>
    </tr>
    {% endfor %}
</tbody>
```

函数可以传参，且支持渲染输出(默认防止xss攻击生效)

```python
# s5.py
from flask import Flask, render_template
app = Flask(__name__)

def f1(arg):
    return "<input type='text' value=%s>" % (arg,)
	# return Markup("<input type='text' value=%s>" % (arg,))
@app.route('/')
def index():
    return render_template('s5.html', func=f1)

if __name__ == '__main__':
    app.run()
```

```html
# s5.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
{#前端使用模版语法，函数传参，Flask默认防止xss攻击关闭开启渲染，使用|safe开启渲染#}
{#前端不使用|safe的话后端导入,使用Markup将需要渲染的字符串包含进去#}
{{ func('lzp')|safe }}
</body>
</html> 
```

2、Flask特有功能—宏定义

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
{#定义宏#}
{% macro xxx(name, type='text', value='') %}
    <p><input type="{{ type }}" name="{{ name }}1" value="{{ value }}" placeholder="测试一"></p>
    <p><input type="{{ type }}" name="{{ name }}2" value="{{ value }}" placeholder="测试二"></p>
    <p><input type="{{ type }}" name="{{ name }}3" value="{{ value }}" placeholder="测试三"></p>
{% endmacro %}

{#使用宏#}
{{ xxx('user') }}
</body>
</html>
```

3、补充说明，关于模版语法的其他，比如说模版的继承与导入，与Django的一模一样，这里就不再阐述，如需查看可以访问博客([Django模版层介绍](https://www.cnblogs.com/suguangti/p/11003655.html))

#### 3-2-5 请求和响应

```python
from flask import Flask
from flask import request
from flask import render_template
from flask import redirect
from flask import make_response
app = Flask(__name__)

@app.route('/login.html', methods=['GET', "POST"])
def login():

# 请求相关信息
# request.quert_string 		url里面的数据
# request.method
# request.args
# request.form				请求体里面的数据
# request.values
# request.cookies
# request.headers
# request.path
# request.full_path
# request.script_root
# request.url
# request.base_url
# request.url_root
# request.host_url
# request.host
# request.files
# 可以拿到文件，并提供保存方法
# obj = request.files['the_file_name']
# 自动生成文件名，防止重复
# obj.save('/var/www/uploads/' + secure_filename(f.filename))

# 响应相关信息
# return "字符串"
# return render_template('html模板路径',**{})
# return redirect('/index.html')
# return jsonify({'k1': 'v1', 'k2': 'v2',...})

# 使用make_response将想要响应的内容打包进response，然后统一返回response
# response = make_response(render_template('index.html'))
# response是flask.wrappers.Response类型
# response.delete_cookie('key')
# response.set_cookie('key', 'value')
# response.headers['X-Something'] = 'A value'
# return response

return "内容"

if __name__ == '__main__':
	app.run()
```

#### 3-2-6 Session

session 对象允许你在不同请求间存储特定用户的信息。它是在 Cookies 的基础上实现的，并且对 Cookies 进行密钥签名要使用会话，你需要设置一个密钥。

```
设置：session['username'] ＝ 'xxx'
删除：session.pop('username', None)
```

先列出简单的session使用，在项目中我们后在后面使用第三方的session组件，所以不多阐述

```
from flask import Flask, session, redirect, url_for, escape, request
 
app = Flask(__name__)
 
@app.route('/')
def index():
    if 'username' in session:
        return 'Logged in as %s' % escape(session['username'])
    return 'You are not logged in'
 
@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
    	# Flask内置的使用加密cookie来保存数据
        session['username'] = request.form['username']
        return redirect(url_for('index'))
    return '''
        <form action="" method="post">
            <p><input type=text name=username>
            <p><input type=submit value=Login>
        </form>
    '''
 
@app.route('/logout')
def logout():
    # remove the username from the session if it's there
    session.pop('username', None)
    return redirect(url_for('index'))
 
# set the secret key.  keep this really secret:
app.secret_key = 'asdfghjkl'

```

### 3-3 

 