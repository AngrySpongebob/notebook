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
      
      ### 4.HttpResponseRedirect
      
      -  重定向， 服务端跳转
      - 构造函数的第一个参数用来指定重定向的地址
      - 案例 ShowViews/views.py
        '''
     
          python 
          url(r'^v10_1/',views.v10_1),
          url(r'v10_2/',views.v10_2),
          url(r'v11/', views.v11, name="v11")
        '''
        '''
        python 
        /east/ShowViews/views中添加以下内容
        
        def v10_1(r):
            return HttpResopseRedirect("/v11")
            
        def v10_2(r):
            return HttpResponseRedirect('v11')
        def v11(r):
            return HttpResponse("这是v11的访问返回")
        '''
      
  ### 5.Request对象
 - Request介绍
    - 服务器接收到HTTP请求协议的请求后，会根据报文创建HttpRequest对象
    - 视图函数的第一个参数是HttpRequest对象
    - 在Django.http，模块中定义了HttpRequest对象的api
 - 属性
    - 下面除特别说明，都是只读的
        - path:一个字符串，表示请求的页面的完整路径，不包含域名
        - method： 一个字符串，表示请求使用的HTTP方法， 常用值包括："GET,"POST
        - encoding:一个字符串，表示提交的数据的编码方式
            - 如果为None，则表示使用浏览器的默认设置，一般为utf-8
            -如果 这个属性是可写的，可以通过修改它来修改访问表单数据使用的编码。
        - GET: 一个类似于字典的对象，包含get请求方式的所有参数
        - POST: 一个类似于字典的对象，包含POST请求的所有参数
        - FILES: 一个类似于字典的对象，包含所有上传的文件
        - COOKIES： 一个标准的Python字典，包含所有的cookie，键和值都为字符串换
        - session：一个既可读又可写的类似于字典的对象，表示当前的会话，只有当Django启用会话的支持时才可用，详细内容见“状态保持”
    - 方法
        - is_ajax(): 如果请求是通过XMLHttpRequest发起的，则返回TRUE
    - QueryDict对象
        - 定义在django.http.QueryDict
        - request对象的属性GET，POST都是QueryDict类型的对象
        - 与Python字典不同，QueryDict类型的对象用来处理同一个键带有多个值的情况
        - 方法get():根据键获取值 
            - 只能获取键的一个值
            - 如果一个键同时拥有多个值，获取最后一个值
        - 方法getlist()：根据键获取值
            - 将键的值以列表返回，可以获取一个键的多个值
      - GET属性
        - QueryDict 类型的对象
        - 包含get请求方式的所有参数
        - 与URL请求地址中的参数对应，位于?后面
        - 参数的格式是键值对，如key1 = value1
        - 多个参数之间，使用&连接，如key1=value1&key2=value2
        - 键是开发人员定下来的，值是可变的
        - 案例 views/v8_get  
    - POST 属性
        - QueryDict类型的对象
        - 包含post请求方式的所有参数
        - 与form表单中的控件对应
        - 表单中必须有name属性，name为键，value为值
            - checkbox存在一键多值的问题
        - 键是开发人员定下的，值是可变的
        - 案例 views/v9_post
            - setting 中设置模板的位置
            - 设置get页面的urls 和函数
               ```python
                # east/urls.py
                # 需要在路由文件中添加两个路由
                url(r'^v9_get',v.v9_get),
                url(r'^v9_post',v.v9_post),
                
                # views.py
                # 在文件中添加下面两个处理函数
                def v9_get(r):
                    return render_to_response("for_post.html")
      
      
                def v9_post(r):
                    rst = ""
                    for k, v in r.POST.items():
                        rst += k + "--->" +v 
                        rst += "<>"    
                    return HttpResponse()      

                ```
                   
- 手动编写视图
    - 实验目的
        - 利用Django快捷函数手动编写 视图处理函数
        - 编写过程中理解视图运行原理
        
        - 分析
            - Django把所有请求信息封装入Request
            - Django通过urls模块把响应请求跟事件处理函数连接起来，并把Request作为参数传入
            - 在相应的处理函数中，我们需要完成两部分
                - 处理业务
                - 把结果封装并返回，我们可用简单的HttpResponse，同样也可以自己处理此功能
            - 本案例不介绍业务处理，把目光集中在如何渲染结果并返回
            
            - render(request, template_name[, context][, context_instance][, content_type])
                - 使用模板和一个给定的上下文环境，返回一个渲染过的HttpResponse对象
                - request: django的传入请求
                - template_name ：模板名称
                - content_instance :上下文环境
                - 案例 teacher/views.py/render_test
            - render_to_response （不经常使用)
                - 根据给定的上下文字典渲染给定模板，返回渲染后的HttpResponse
    - 系统内建视图
        - 可以直接使用
        - 404
            - default.page_not_found(request, template_name='404.html)
            - 系统引发Http404时触发
            - 默认request_path 变量给模板， 即导致错误的URL
            - DEBUG = True则不会调用404，取而代之的是调试信息
            - 404视图会被传递给一个RequestContext对象，并且可以翻个文模板上下文处理器提供的变量
            
        - 500（server error)
            - defaults.server_error(request, template_name="500.html")
            - 需要DEBUG=False,否则不调用
        - 403(HTTP Forbidden) 视图
            - defaults.permission_denied(request, template_name="403.html")
            - 通过PermissionDenied触发
        - 400(bad request)
            - defaults.bad_request(request, template_name='400.html')
            - DEBUG = False
## 基于类的视图
- 和基于函数的视图的优势和区别       
    - HTTP方法的method可以有各自的方法，不需要使用条件分支来解决
    - 可以使用面向对象
- 概述
    - 核心是允许使用不同的实例方法来响应不同的HTTP请求方法，而避开条件分支实现
    - as_view 函数作为类的可调用入库， 该方法创建一个实例并调用dispatch方法按照请求方法
    
- 类属性使用
    - 在类定义时覆盖
    - 在调用as_view 的时候直接作为参数使用，例如，
    ```python
      urlpatterns = [
          url(r'^about/', GreetingView.as_view(greeting='G"day'))
    ]
    ```
    - 在基于类的视图的扩充大致有三种方法：Mixin,装饰as_view,装饰dispatch
    - 使用Mixin
        -多继承的一种形式，来自父类的行为和属性组合在一起
        - 解决多继承问题
        - View的子类只能单继承， 多继承会导致不可期问题
        - 多继承带来的问题
            - 结构复杂
            - 
            
            
#Models 
- ORM
     - ObjectRelationMap:把面向对象思想转化成关系型数据库思想
        - 类对应数据库中的表
        - 类的实例对应数据库中的一条记录
        - 类中的属性对应数据库中的字段
     -  在应用中的models.py文件中定义class
     - 所有需要使用orm的class都必须是models.Model 的子类
     - class中的所有属性对应表格中的字段
     - 字段的类型都必须使用models.xxx  不能使用Python中的类型
     - 在Django中，Models负责与数据库交互
- Django链接数据库 
    - 自带默认的数据库sqllite3
        - 关系型数据库
        - 轻量级
    - 建议开发用SQLLITE3， 部署用MySQL之类的数据库
    - 以下代码在settings中进行设置
        ```
          #Django连接数据库 
          DATABASS = {
          'default' : {
              "ENGINE":'django.db.backends.mysql',
              "NAME":'数据库名',
              "PASSWORD": '数据库密码',
              "HOST": '127.0.0.1',
              "PORT": '3306',
              'USER':'root',
          }
        }
        ``` 
    - 需要在项目文件下的__init__ 文件中导入pymysql包
    
    
        import pymysql
        pymysql.install_as_MySQLdb()
        
    - 对现有的数据库进行操作
    python manage.py inspectdb >models.py  
- models类的使用
    - 定义和数据库表映射的类
        - 在应用中的models.py文件中定义class
        - 所有需要使用orm的class都必须是models.Model的子类
        - class中的所有属性对应表格中的字段
        -字段的类型都必须采用 models.xxx 不能使用Python中的类型
- 字段常用参数
    1.max_length:规定字符串的最大长度
    2.blank：是否允许字段为空，默认不允许（bool)
    3.null:在db中控制是否保存为null, 默认为false
    4.default:默认值
    5.unique:唯一
    6.verbose_name:假名
- 数据库的迁移
    1.
    ```
      python manage.py makemigrations  # 在终端中运行
    ```
    ps: 如果迁移中出现没有变化或报错， 可以尝试强制迁移
    2.
    ```
    # 强制迁移命令
    python manage.py makemigrations 应用名
    python manage.py migrate (应用名)
      
    ```
    3. 对于默认数据库为了避免出现混乱，如果数据库中没有数据，可以将自带的恶sqllite3数据库删除 app中的migrations删除
##### 查看数据库中的数据  
```
1. 启动命令行：python manage.py shell（如果models.py的代码改变需要进行重启shell）
ps:对orm的操作分为静态函数和非静态函数两种，静态是指在内存中只有一份，非静态每个实例有一份
2. 在命令行中导入对应的映射类
    from 应用.models import 类名
3.使用 objects 属性操作数据库， objects是 模型中实际和数据库进行交互的
4.查询命令
 - 类名.objects.all()  查询数据表中的所有数据，返回结果是一个QuerySet 对象
 - 类名.objects.filter(条件[age=18])  # 进行过滤。相当于sql中的where
 - 
5.插入数据 
>>> xiaoming = Teacher()  # 实例化Teahcer类
>>> xiaoming.name = "aa"  # 给属性赋值
>>> xiaoming.age = 33
>>> xiaoming.address = "dddddd"
>>> xiaoming.save()   # 保存到数据库中
>>> ta = Teacher.objects.all()  # 这是一个特殊的集合
>>> ta[0]  # 查询第一条数据
>>> ta[0].name # 查询第一条数据中的name属性

``` 
- 常见的查找方法
1. 通用查找格式：属性名__(用下面的内容) = 值 ，
 例如：Teacher.objects.filter(age__gt=18) # 查询年龄大于18的记录
 - gt : 大于
 - gte : 大于等于
 - lt : 小于
 - lte : 小于等于
 - range : 范围  
 - year : 年份
 - isnull : 是否为空
2. 查找等于指定值的格式：属性名 = 值
3. 模糊查找： 属性名__(使用下面的内容) = 值
 
 
     * exact : 精确等于
     * leact : 不区分大小写
     * contains: 包含  例如查询包含字母的老师 ta = Teacher.objects.filter(course__contains = 'l')
     * startwith : 以...开头
     * endwith: 以...结尾
    