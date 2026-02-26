在 Windows 上搭建 Linux 开发环境，跑通 Django
📝 一句话总结

WSL 让 Windows 和 Linux 完美融合——在 Ubuntu 里装 Django，用 Windows 浏览器访问，文件两边共享，开发体验无缝衔接。

🧱 环境架构图
````
Windows 10
    ↓
[Docker Desktop] 
    ↓
[WSL 2] ← 轻量级 Linux 子系统
    ↓
[Ubuntu 24.04] ← 真正的 Linux 环境
    ↓
[Python + Django] ← 开发框架
    ↓
[Windows 浏览器] ← 通过 localhost 访问
````

📦 安装与配置全流程 

安装 WSL 和 Ubuntu

powershell

在 Windows PowerShell（管理员）中执行

wsl --install

自动下载并安装 Ubuntu

设置用户名（如 yangcheng）和密码

安装完成后，从开始菜单打开 Ubuntu

在 Ubuntu 中安装 Django

1,更新系统

sudo apt update

安装 Python 和虚拟环境工具

sudo apt install python3 python3-pip python3-venv -y

创建项目目录

mkdir my_blog
cd my_blog

创建并激活虚拟环境

python3 -m venv venv

source venv/bin/activate   # 激活后提示符出现 (venv)

安装 Django

pip install django

验证安装

django-admin --version     # 看到 6.0.2 说明成功

创建项目

django-admin startproject my_blog .

启动开发服务器

python manage.py runserver


在 Windows 浏览器访问

打开 Chrome/Edge

访问 http://localhost:8000

看到 Django 欢迎页面 🎉


🔍 关键机制理解

文件系统：两边互访

| 位置 | Linux 路径 | Windows 访问方式 |
|------|-----------|------------------|
| Linux 文件 | `/home/yangcheng/my_blog/` | `\\wsl.localhost\Ubuntu\home\yangcheng\my_blog` |
| Windows 文件 | `/mnt/c/Users/admin/Desktop/` | `C:\Users\admin\Desktop` |


网络端口：自动转发

| 服务位置 | 访问方式 | 原理 |
|---------|---------|------|
| Ubuntu 里运行 | 127.0.0.1:8000 | Django 监听 Linux 端口 |
| Windows 访问 | localhost:8000 | WSL 自动转发到 Linux |

验证：在 Ubuntu 和 Windows 同时 ping localhost，都能通。


✅ 安装验证清单

| 验证项 | 命令 | 预期结果 |
|--------|------|----------|
| WSL 版本 | `wsl -l -v` | Ubuntu 版本为 2 |
| Ubuntu 运行 | `uname -a` | 显示 Linux 内核信息 |
| Python 版本 | `python3 --version` | 3.10+ |
| 虚拟环境 | `which python` | 路径在 `venv/bin/python` |
| Django 版本 | `django-admin --version` | 6.0.2 |
| 服务可访问 | 浏览器打开 `localhost:8000` | 看到 Django 欢迎页 |


🎯 和 Docker 的关系 

| 技术 | 作用 | 互补关系 |
|------|------|---------|
| Docker | 容器化部署，环境隔离 | 适合上线运行 |
| WSL | 本地开发环境，贴近 Linux | 适合日常开发 |

最佳实践：

开发用 WSL + Ubuntu + 虚拟环境

部署用 Docker + 生产镜像

⚠️ 常见问题与解决

| 问题 | 原因 | 解决 |
|------|------|------|
| 服务跑着但浏览器访问不了 | 没在虚拟环境启动 | `source venv/bin/activate` 再启动 |
| 文件在 Windows 修改后权限乱了 | 跨系统权限问题 | 尽量在 Linux 里操作文件 |
| WSL 关机后服务没了 | WSL 不自动启动 | 重新运行 `python manage.py runserver` |
| 无法安装 mysqlclient | 缺依赖 | `sudo apt install python3-dev default-libmysqlclient-dev` |

✅ 一句话记住

WSL + Ubuntu + Django = 用 Windows 的舒服，写 Linux 的代码——开发环境无缝集成，想写就写，想跑就跑。