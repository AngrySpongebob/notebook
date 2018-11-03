# Django

- 环境
   - python3.6
   - django1.8

- 参考资料
  - yiyibooks.cn

## 环境搭建

  - conda list 显示当前环境安装的包
  - conda env list 显示安装的虚拟环境列表
  - conda create -n name python=3.6 创建一个python版本为3.6的名字为name的虚拟环境
  - (Linux)source activate env_name 激活环境
  - (Windows)activate env_name      激活环境
  - pip install django==1.8 安装版本为1.8的Django



##  后台需要的流程
 - 路由
 - 视图(views处理业务逻辑)
 - 模型（models）> 与数据库进行交互
 - 模板(templete 页面)

## 创建第一个程序
 - django-admin startproject project-name
 - 目录结构
 - project-name
   - project-name
     - settings.py 配置文件
     - urls.py 路由
     - wsgi.py 通用网关
   - manager.py 命令行处理
### 启动 
 - python manage.py runserver  命令行下的启动
 - pycharm 启动
   - Files - settings - project - project interpxeter  - conda enviroment existing enviroment - anaconda安装目录下的envs中的解释器

# 路由系统-urls.py
- 创建app
  - app:负责一个具体业务或者一类业务的模块
  - python manage.py starttapp app-name
- 路由
  - 按照具体的请求URL，导入到相应的业务处理模块的一个功能
  - django的信息控制中枢
  - 本质上是接收的URL和相应的处理模块的一个映射
  - 在接收URL的请求的匹配上使用了正则
  - URL的具体格式在urls.py中表示
- 需要关注的两点
  - 1.接收的URL是什么？即如何对传入的URL进行匹配
  - 2.已知URL匹配到哪个处理模块

- URL匹配规则
 - 从上往下依次进行比对
 - URL格式是分级的，按照一级一级向下比对
 - 子URL一旦被调用，则不会返回到主URL
 - 正则以r开头，表示不需要转义，注意尖号（^）和美元符号（$，以什么结尾）
   - 'one/two/three' r'^one/'   匹配成功
   - 'oo/one/two/three'  r'^one/' 不匹配
   - 'one/two/three/'  r^three/$'  匹配成功  
   -  'one/two/three/oo/'  r'three/$'不匹配
   - 开头不需要有反斜杠
 - 如果从始至终都没有匹配到，报错
## url在app中处理
  - 如果所应用URL都集中tuilingxueyuan/urls.py中，可能导致文件的臃肿
  - 可以把urls具体功能分散到每个app中
      - 从django.conf.urls 导入include
      - 注意此时正则部分的写法
      - 添加include导入每个app的路由，每个app的路由来分别处理这个app下的各种事务
      - 需要在每个app中创建一个路由文件
      """
      # 主路由
          from django.conf.urls import include,url
          from teacher import teacher_url  //导入子路由


          url(r'^teacher/,include(teacher_url)) //导入名字为teacher_url.py的子路由

      # app下的子路由
        from django.conf.urls import include, url
        from django.contrib import admin

        from . import views  #要注意

        urlpatterns = [
          url(r'liming/', views.do_func),  # 正则前面没有 ^
        ]

      """
  - 同样可以使用参数

#### url中的嵌套参数
- 捕获某个参数的一部分
- 例如url /index/page-3, 需要捕获数字3作为参数
  """
    url(r'index_1/(page-(\d+)/)?$', sv.myindex_1), # not good
    url(r'index_2(?:page-(?P<page_number>\d+)/?$',sv.myindex_2), #好 ?:后面的内容被忽略， ?P表示后面的东西是参数 \d+表示至少有一个数字

  """
- 上述例子会得到两个参数，但?:会忽略

### 6.传递额外参数
 - 参数不仅来自于url，还可能是自己定义的内容
  '''
    url(r'^extrem/$',sv.extremParm,{'name':'liuming'}),  # 传递的自定义参数是字典格式

  '''

- 附加参数同样适用于include语句，此时对include内所有都添加

#### 7.url的反向解析

- 防止硬编码
- 本质上是对每个url进行命名
- 以后再编码代码中使用url的值，原则上都应该使用反向解析

# views 视图
- 视图即视图函数，接收web请求并返回web响应的事物处理函数，
- 响应指的是符合HTTP协议要求的任何内容，包括json/string/html等
- 本章忽略事务处理，终点在如何返回处理结果上
- 其他简单的视图
  - 通过查看django.http 可以知道提供了好多与HttpRespose类似的简单视图
  - 注意：Http404为Exception子类，需要加上raise Http404
  '''
    # 视图函数
    def v2_exp(r):
      #执行这个函数以后页面就会出现404的错误
      raise Http404
      return HttpResponse("success")

  '''

  - HTTPResponse 详解
    - 方法
      -init  使用网页内容实例化HTTPResponse对象
      - write(content) 以文件的方式写
      - flush()  以文件的形式输出缓存区
      - set_cookie(key,value="",max_age=None,expires=None):设置cookie
        - key，value都是字符串类型、
        - max_age 是一个整数，表示在指定秒数后过期
        - expires是一个datetime或timedelta对象，会话将会在这个指定的日期/时间过期
        - max_age / expires 二选一
        - 如果不指定过期时间，则在两个星期后过期
      - delete_cookie(key), 删除指定的key的cookie，如果key不存在则什么也不发生
      - session放在服务端中，cookie放在客户端中
      - cookie中不能放用户名密码等敏感的东西
