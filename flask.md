# Flask

## http的请求与响应
### flask生命周期
客户端 -> wsgi应用程序 -> 全局钩子 -> 路由 -> 视图 -> 路由 -> 全局钩子 -> wsgi应用程序 -> 客户端

### 请求



#### flask g


#### jinja2模板
flask内置的模板语言，设计思想来源于Django的模板引擎DTP，并拓展了其语法和一系列强大的功能。
- Flask提供的render_templates函数封装了该模板引擎Jinja2
- render_templates函数的第一个参数是模板的文件名，后面的参数是键值对，表示模板中变量对应的数据值。
- 在模板中可以使用{% autoescape %}来手动设置是否转义。
- Flask在Jinja2环境中加入一些全局函数和辅助对象来增强模板的功能。

## day4
### 4.1模板输出变量
{{变量名}} 这种语法叫做变量代码块

**locals()

### 4.2流程控制语句
- if/elif/else/endif
- for/else/endfor
#### if语句
Jinja2语法中的if语句和Python的if语句相似，用{%%}定义的控制代码块。

### 4.3过滤器
```jinja
<th>{{"%.2f" | format(book.price)}}</th>
<th>{{ book.cover | safe }}</th>
```

### 4.5模板继承
在模板中，可能会遇到以下情况
- 多个模板具有完全相同的顶部和底部内容
- 多个模板中具有相同的模板代码内容，但是内容中部分值不一样
- 多个模板具有完全相同的html代码块，侧边栏

模板继承是为了重用模板中的公共内容，一般web开发中，继承主要使用在网站的顶部菜单、底部、弹窗。这些内容可以定义在父模板中，子模版直接继承，而不需要重复书写。


### 4.6 CSRF攻击防范
CSRF：跨域请求伪造攻击

```bash
pip install flask_wtf
```
```python
from flask_wtf.csrf import CsrfProtect
app = Flask(__name__)
app.config['SECRET_KEY'] = 'mykey'

csrf = CsrfProtect()
csrf.init_app(app)
# or CsrfProtect(app)
```
Note: 你需要为CSRF保护设置一个密钥。通常下，同Flask应用的SECRET_KEY是一样的。

- 如果模板中存在表单，你不需要做任何事情。与之前一样：
    ```html
    <form method='post' action='/'>
        {{ form.csrf_token }}
    </form>
    ```
- 如果模板中没有表单，你仍然需要一个CSRF令牌
    ```html
    <form method='post' action='/'>
        <input type='hidden' name='csrf_token' value='{{ csrf_token() }}'/>
    </form>
    ```

## day5
###  5.1 SQLALchemy的基本使用、Flask使用SQLALchemy连接数据库并创建模型

#### ORM
全称Object-Relation Mapping，中文**对象-关系映射**，主要实现模型对象到关系数据库数据的映射。

优点：
 - 只需要面对对象编程，不需要面向数据库编写SQL

  - 对数据库的操作都转化成对类/对象的属性和方法的操作，表字段 ---> 对象属性， sql关键字 ---> 操作方法

  - 不用编写原生sql语句，当然复杂情况也支持编写原生sql

- 实现了数据模型代码与数据库数据的解耦，屏蔽了不同数据库操作上的差异

  - 不再需要关注当前项目使用的是哪种数据库

  - 通过简单的配置就可轻松实现更换数据库，而不需要修改业务代码，

缺点：
  - 相比较直接使用sql语句操作数据库，ORM需要把操作转换成sql语句，所以有性能损失

  - 根据对象的操作转换成sql语句，根据查询的结果转化成模型实例对象，在映射过程中有性能损失

  - 不同的ORM提供的操作不一样，增加了学习成本

#### Flask-SQLALchemy
