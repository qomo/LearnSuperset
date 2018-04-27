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



## 