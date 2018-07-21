# 学习Superset  
Superset是一个灵活的BI系统，现在公司里主要打算用做测试数据的可视化。  
## Superset环境的搭建（docker）  
1. 假设已经完成[docker环境的安装](https://docs.docker.com/install/)  
2. 在公司内需要设置代理，以便能够顺利获取到docker镜像  
3. 拉取这个[superset镜像](https://hub.docker.com/r/amancevice/superset/)
<pre><code>
# docker pull amancevice/superset
</code></pre>
4. 启动镜像(`docker run --help`指令可获取参数帮助文档)
<pre><code>
# docker run -p 8088:8088 -v /dockerfs/superset/conf:/etc/superset -v /dockerfs/superset/data:/data  --name superset -d amancevice/superset
</code></pre>
> 这时候访问ip_of_host:8088就能看到superset服务已经启动了，但是需要登陆。首先需要添加一个管理员。([参考官方文档](https://superset.incubator.apache.org/installation.html))  
5. 进入docker容器交互界面  
<pre><code>
# docker exec -it superset bash
</code></pre>
6. 初始化superset环境，主要是创建管理员账户
<pre><code>
# superset-init
</code></pre>
> 这时候就可以用你创建的账户登陆了，但是现在superset系统里什么数据都没有，你可能不知道它可以做什么。  
7. 下载example  
<pre><code>
# superset load_examples
</code></pre>

## 案例XXXX数据分析  
> 保密😁  

## 开发环境搭建(On Mac)  
1. 下载源码  
<pre><code>
# git clone https://github.com/apache/incubator-superset
</pre></code>  
> 因为墙的原因，可能你无法clone下来源码，也可以选择在web页面下载压缩包

2. 参考[官方安装指南](https://superset.incubator.apache.org/installation.html)的**Making your own build**小节编译源码  
<pre><code>
# assuming $SUPERSET_HOME as the root of the repo
cd $SUPERSET_HOME/superset/assets
yarn
yarn run build
cd $SUPERSET_HOME
python setup.py install
</code></pre>  

3. 参考**Superset installation and initialization**小节初始化运行  

## Superset目录结构  
> 参考：[Superset: Just Enough](https://zhuanlan.zhihu.com/p/30562131)
<pre><code>
├── app
│   └── static
├── assets                      # js jsx 等前端库
│   ├── branding
│   ├── dist
│   ├── images
│   ├── node_modules
│   ├── spec
│   ├── src
│   ├── stylesheets
│   ├── vendor
│   ├── backendSync.json
│   ├── docs -> ../../docs/_build/html/
│   ├── js_build.sh
│   ├── package-lock.json
│   ├── package.json
│   ├── version_info.json
│   ├── webpack.config.js
│   └── yarn.lock
├── bin
│   ├── __init__.py
│   └── superset
├── connectors
│   ├── base
│   ├── druid
│   ├── sqla
│   ├── __init__.py
│   └── connector_registry.py
├── data
│   ├── __init__.py
│   ├── airports.csv.gz
... ...
├── db_engines
│   ├── __init__.py
│   └── hive.py
├── migrations                  # 数据库升级脚本
│   ├── versions
│   ├── README
│   ├── __init__.py
│   ├── alembic.ini
│   ├── env.py
│   └── script.py.mako
├── models                      # 基于SQLSQLAlchemy定义的模型，建立ORM映射
│   ├── __init__.py
│   ├── annotations.py
│   ├── core.py
│   ├── helpers.py
│   └── sql_lab.py
├── static
│   └── assets -> ../assets
├── templates                   # jinjia2模板
│   ├── appbuilder
│   ├── email
│   ├── superset
│   └── index.html
├── translations
│   ├── de
│   ├── en
... ...
│   ├── zh
│   ├── __init__.py
│   ├── babel.cfg
│   ├── messages.pot
│   └── utils.py
├── views                       # 基于Flask AppBuilder的实图
│   ├── __init__.py
│   ├── annotations.py
│   ├── base.py
│   ├── core.py
│   ├── sql_lab.py
│   └── utils.py
├── __init__.py                 # superset的主模块，Flask app 和 appbuilder 都在这里创建
├── cache_util.py
├── cli.py                      # 通过Manager来管理app的启动指令
├── config.py                   # 配置文件
├── dataframe.py                # dataframe数据结构的封装
├── db_engine_specs.py          # Compatibility layer for different database engines
├── dict_import_export_util.py
├── exceptions.py
├── extract_table_names.py
├── forms.py
├── import_util.py
├── jinja_context.py
├── legacy.py
├── security.py
├── sql_lab.py
├── sql_parse.py
├── stats_logger.py
├── utils.py
└── viz.py                      # This module contains the Visualization objects
</code></pre>
> 参考：[Appbuilder的Hello World](http://flask-appbuilder.readthedocs.io/en/latest/)  
app = Flask(__name__)  
app.config.from_object('config')  
db = SQLA(app)  
appbuilder = AppBuilder(app, db.session)  

> 参考：Manager的定义与注释(site-packages/Flask_Script-2.0.6-py3.6.egg/flask_script/__init__.py)  
使用了三种添加命令或命令行控制参数的方式:
> 1. manager.add_command
> 2. @manager.command
> 3. @manager.option

## 一个Slice的响应过程  

### 主要接口  
> superset/views/core.py    

Class Superset提供了用户交互的基本视图，其中主要的接口为:  
- @expose('/welcome') --- 欢迎界面
- @expose('/explore/<datasource_type>/<datasource_id>/', methods=['GET', 'POST']) --- Slice页面
- @expose('/profile/<username>/') --- 用户页面
- @expose('/sqllab') --- SQL编辑器页面  
- @expose('/dashboard/<dashboard_id>/') --- 看板页面

这几个接口，最终都会渲染并返回一个superset/basic.html模版，如：
<pre><code>
def explore(self, datasource_type=None, datasource_id=None):
    ...
    ...
    return self.render_template(
        'superset/basic.html',                      # jinja模板路径
        bootstrap_data=json.dumps(bootstrap_data),  # 和bootstrap相关？暂时没搞清楚？？？
        entry='explore',                            # 指明将要加载的js/css脚本
        title=title,                                # 标题
        standalone_mode=standalone)
</code></pre>  
> dashboard渲染的是dashboard.html模板，但其也继承至basic.html  
### Entry  
上一小节调用的self.render_template函数要传的参数包括模板路径basic.html，和entry。entry是basic.html模板将要使用的一个参数，在basic.html中相关内容如下：
```
... ...
      {% if entry %}
        <link rel="stylesheet" type="text/css" href="{{ js_manifest(entry + '.css') }}" />
      {% endif %}
      ...
      {% if entry %}
        <script src="{{ js_manifest(entry + '.js') }}"></script>
      {% endif %}
... ...
```
entry加上'.css'/'.js'后形成新的参数传入js_manifest函数，这个函数将返回对应的css/js静态文件路径。  
> 在模板中使用的js_manifest函数是在superset/__init__.py中被注册的，具体实现请细看这个文件中的定义。  

各css/js脚本在superset/static/assets/dist/路径下。  
其中，js是由Npm run dev-fast命令，按superset/assets/webpack.config.js配置的js源码目录打包生成的。
```
# in webpack.config.js
 entry: {
    theme: APP_DIR + '/src/theme.js',
    common: APP_DIR + '/src/common.js',
    addSlice: ['babel-polyfill', APP_DIR + '/src/addSlice/index.jsx'],
    explore: ['babel-polyfill', APP_DIR + '/src/explore/index.jsx'],
    dashboard: ['babel-polyfill', APP_DIR + '/src/dashboard/index.jsx'],
    sqllab: ['babel-polyfill', APP_DIR + '/src/SqlLab/index.jsx'],
    welcome: ['babel-polyfill', APP_DIR + '/src/welcome/index.jsx'],
    profile: ['babel-polyfill', APP_DIR + '/src/profile/index.jsx'],
  },
```

## FAQ  
在win下编译，
python setup.py build的过程中，有可能遇到/superset/static/assets/package.json文件不存在的问题
解决方法是：
到static目录下，通过命令
```
mklink /D assets ..\assets
```
[创建目录的软连接](https://www.howtogeek.com/howto/16226/complete-guide-to-symbolic-links-symlinks-on-windows-or-linux/)