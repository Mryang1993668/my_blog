📌 环境概述
操作系统：Windows 10

项目路径：C:\work\develop\my_blog

Python 版本：3.14

Django 版本：6.0.2

MySQL 版本：9.6.0

数据库驱动：pymysql

一、Django 环境搭建

1. 创建项目

安装 Django

pip install django

创建项目（注意最后的点 .）

django-admin startproject myblog .

```查看生成的文件
# my_blog/
# ├── manage.py
# ├── myblog/
# │   ├── __init__.py
# │   ├── settings.py
# │   ├── urls.py
# │   └── wsgi.py
# └── db.sqlite3（初始 SQLite 数据库）
```

Django 核心命令

启动开发服务器

python manage.py runserver

创建应用

python manage.py startapp blog

生成迁移文件

python manage.py makemigrations

执行迁移（建表）

python manage.py migrate

创建超级管理员

python manage.py createsuperuser

进入 Django 数据库命令行

python manage.py dbshell


Django MTV 架构理解

| 组件 | 作用 | 文件位置 |
|------|------|----------|
| **Model（模型）** | 定义数据结构 | `blog/models.py` |
| **Template（模板）** | 展示页面 | `blog/templates/` |
| **View（视图）** | 业务逻辑 | `blog/views.py` |
| **URL（路由）** | 地址映射 | `myblog/urls.py` |

执行流程：

text 
```
浏览器请求 → urls.py → views.py → models.py → 数据库
        ← template ← views.py ← 数据库返回数据
```

二、MySQL 安装与配置
1. 下载安装 MySQL
官网下载：https://dev.mysql.com/downloads/mysql/

选择：Windows (x86, 64-bit), MSI Installer

安装类型：Developer Default

关键配置：

Port: 3306

Authentication: Use Legacy Authentication Method（为了兼容性）

Root 密码：设置并记住

2. 验证 MySQL 安装

登录 MySQL

mysql -u root -p

查看数据库

mysql> SHOW DATABASES;
````
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
````

3. 创建 Django 项目数据库

sql
````
mysql> CREATE DATABASE my_blog_db 
       CHARACTER SET utf8mb4 
       COLLATE utf8mb4_unicode_ci;

mysql> SHOW DATABASES;  -- 确认 my_blog_db 已创建
````

三、Django 连接 MySQL

安装数据库驱动

由于 mysqlclient 在 Windows + Python 3.14 环境下可能有问题，改用 pymysql：


pip install pymysql

配置 Django 使用 MySQL

步骤1：创建 myblog/__init__.py（如果不存在），添加：

python

import pymysql

pymysql.install_as_MySQLdb()

步骤2：修改 myblog/settings.py 中的数据库配置：
````
python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'my_blog_db',
        'USER': 'root',
        'PASSWORD': '你的密码',  # 替换为实际密码
        'HOST': '127.0.0.1',
        'PORT': '3306',
        'OPTIONS': {
            'charset': 'utf8mb4',
        }
    }
}
````

迁移数据到 MySQL

在 MySQL 中创建所有 Django 需要的表

python manage.py migrate

成功输出（看到所有 OK）：

````
text
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  ...（全部 OK）
````

创建超级用户

python manage.py createsuperuser

按提示输入用户名、密码

四、验证切换成功

在 MySQL 中查看数据

mysql -u root -p

USE my_blog_db;

SHOW TABLES;

应该看到 Django 创建的所有表：

````
text
+----------------------------+
| Tables_in_my_blog_db       |
+----------------------------+
| auth_group                 |
| auth_group_permissions     |
| auth_permission            |
| auth_user                  |
| django_admin_log           |
| django_content_type        |
| django_migrations          |
| django_session             |
+----------------------------+
````

查看用户表

```sql
mysql> SELECT id, username, is_superuser FROM auth_user;
+----+----------+--------------+
| id | username | is_superuser |
+----+----------+--------------+
|  1 | 123456   |            1 |
+----+----------+--------------+
```

启动 Django 验证

python manage.py runserver

访问 http://127.0.0.1:8000/admin/

用刚创建的账号登录


五、关键命令速查表

Django 命令

| 命令 | 作用 |
|------|------|
| `python manage.py runserver` | 启动开发服务器 |
| `python manage.py startapp 应用名` | 创建新应用 |
| `python manage.py makemigrations` | 生成迁移文件 |
| `python manage.py migrate` | 执行迁移（建表） |
| `python manage.py createsuperuser` | 创建超级管理员 |
| `python manage.py dbshell` | 进入数据库命令行（SQLite） |

MySQL 命令

| 命令 | 作用 |
|------|------|
| `mysql -u root -p` | 登录 MySQL |
| `SHOW DATABASES;` | 查看所有数据库 |
| `CREATE DATABASE 库名;` | 创建数据库 |
| `USE 库名;` | 切换到指定数据库 |
| `SHOW TABLES;` | 查看当前库所有表 |
| `SELECT * FROM 表名;` | 查询表数据 |
| `EXIT;` | 退出 MySQL |

六、常见问题与解决

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `'mysql' 不是内部或外部命令` | MySQL 没加到环境变量 | 手动添加 `C:\Program Files\MySQL\MySQL Server 9.6\bin` 到 PATH |
| `No module named 'mysql'` | 数据库驱动没装好 | 安装 `pymysql` 并在 `__init__.py` 中配置 |
| `ERROR 1064 (42000)` | SQL 语句没加分号 | 所有命令末尾加 `;` |
| `Access denied for user` | 用户名或密码错误 | 检查 settings.py 中的密码 |
| `Unknown database 'my_blog_db'` | 数据库没创建 | 在 MySQL 中先创建数据库 |

七、当前项目结构

```text
my_blog/
├── .venv/                 # 虚拟环境
├── blog/                  # 博客应用
│   ├── migrations/        # 迁移文件
│   ├── __init__.py
│   ├── admin.py           # 后台配置
│   ├── apps.py
│   ├── models.py          # 数据模型
│   ├── tests.py
│   └── views.py           # 视图函数
├── myblog/                 # 项目配置
│   ├── __init__.py        # 已添加 pymysql 配置
│   ├── settings.py        # 已改为 MySQL 连接
│   ├── urls.py            # 主路由
│   └── wsgi.py
├── manage.py              # 项目管理
└── db.sqlite3             # 原来的 SQLite 数据库（可保留备份）
```

八、总结要点

Django 的 MTV 架构：Model（数据）、Template（展示）、View（逻辑）分离

数据库切换本质：只需改 settings.py 中的 DATABASES 配置

MySQL 关键点：

需要先安装 MySQL 服务

需要安装数据库驱动（pymysql）

需要在 MySQL 中先创建数据库

迁移命令：makemigrations（生成）→ migrate（执行）

数据验证：可以通过 Django 后台或 MySQL 命令行查看

下一步

创建文章模型：在 blog/models.py 中定义 Post 类

编写视图函数：从数据库取数据显示到页面

表单：文章发布功能

探索更多：Django REST framework、用户认证、文件上传等