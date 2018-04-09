# 学习Superset  
Superset是一个灵活的BI系统，现在公司里主要打算用做测试数据的可视化。  
## Superset环境的搭建（docker）  
1. 假设已经完成[docker环境的安装](https://docs.docker.com/install/)  
2. 在公司内需要设置代理，以便能够顺利获取到docker镜像  
3. 拉取这个[superset镜像](https://hub.docker.com/r/amancevice/superset/)
<pre><code>
# docker pull amancevice/superset
</code></pre>
4. 启动镜像(`docker run --help`指令可获取参数帮助文档)
<pre><code>
# docker run -p 8088:8088 -v /dockerfs/superset/conf:/etc/superset -v /dockerfs/superset/data:/data  --name superset -d amancevice/superset
</code></pre>
> 这时候访问ip_of_host:8088就能看到superset服务已经启动了，但是需要登陆。首先需要添加一个管理员。([参考官方文档](https://superset.incubator.apache.org/installation.html))  
5. 进入docker容器交互界面  
<pre><code>
# docker exec -it superset bash
</code></pre>
6. 初始化superset环境，主要是创建管理员账户
<pre><code>
# superset-init
</code></pre>
> 这时候就可以用你创建的账户登陆了，但是现在superset系统里什么数据都没有，你可能不知道它可以做什么。  
7. 下载example  
<pre><code>
# superset load_examples
</code></pre>

## 案例XXXX数据分析  
> 保密😁  
