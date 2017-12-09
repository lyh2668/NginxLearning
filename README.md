### 准备工作
#### 环境
- CentOS 7
- 阿里云服务器

#### 环境确认
1. 网络
2. yum
3. 关闭iptables
- `iptables -L` 查看是否存在iptables规则 `List the rules in a chain or all chains`
- `iptables -F` 删除所有iptables规则 `Delete all rules in  chain or all chains`
4. 停用selinux
- `getenforce` 查看selinux是否开启
- `setenforce 0` 关闭selinux `usage:  setenforce [ Enforcing | Permissive | 1 | 0 ]`
#### 环境安装
```
yum -y install gcc gcc-c++ autoconf pcre pcre-devel make automake
yum -y install wget httpd-tools vim
```
- `gcc` C编译器
- `gcc-c++` C++编译器
- `make` Makefile编译器
- `automake` & autoconf 自动生成makefile
- `pcre` nginx rewrite依赖于PCRE库
- `pcre-devel` 编译需要头文件，devel库包含头文件
- `httpd-tools` http相关工具包

#### 建议目录结构
```
opt
├── app
├── backup
├── downloads
├── logs
└── work
```

### Nginx
#### 一. Nginx 简介
> 一个开源且高性能、可靠的HTTP中间件、代理服务。

#### 二. 常见的HTTP服务
- HTTPD - Apache
- IIS - 微软
- GWS - Google

#### 三. 为什么选择Nginx
1. IO多路复用epoll
>  I/O multiplexing 这里面的 multiplexing 指的其实是在单个线程通过记录跟踪每一个Sock(I/O流)的状态(对应空管塔里面的Fight progress strip槽)来同时管理多个I/O流
- IO多路复用模型
    - select
        - 线性遍历条件低下
        - 文件描述符最大限制为1024
        - 非线程安全的
    - poll
    - epoll
2. 轻量级
    - 功能模块少
    - 代码模块化
3. CPU亲和（affinity）
> 把CPU核心和Nginx工作进程绑定方式，把每个worker进程固定在一个CPU核心上执行，减少切换CPU的cache miss，获得更好的性能。

`worker_processes` 应该和CPU核心数相等时为最佳值
4. sendfile
- 常规： 资源——>内核空间——>用户空间——>内核空间——>Socket
- sendfile： 资源——>内核空间——>Socket

从而减少了开销
#### 基于Nginx的中间件架构
1. 安装
`yum install -y nginx`
2. 目录讲解
- 查看目录 `rpm -ql nginx`
    -  配置文件

路径 | 类型 | 作用
-|-|-|
/etc/logrotate.d/nginx | 配置文件 | nginx日志轮转，用于logrotate服务的日志切割
/etc/nginx/nginx.conf<br>  /etc/nginx/nginx.conf.default<br>  /etc/nginx/conf.d<br>  /etc/nginx/default.d | 目录、配置文件 | Nginx主配置文件
/etc/nginx/fastcgi_params<br>  /etc/nginx/uwsgi_params<br>  /etc/nginx/scgi_params | 配置文件 | cgi配置相关，fastcgi配置
/etc/nginx/koi-utf<br>  /etc/nginx/koi-win<br>  /etc/nginx/win-utf | 配置文件 | 编码转换映射转化文件
/etc/nginx/mime.types | 配置文件 | 设置http协议的Content-Type与扩展名对应关系
/usr/lib/systemd/system/nginx.service | 配置文件 | 用于配置出系统守护进程管理器管理方式
/usr/lib64/nginx/modules | 目录 | Nginx模块目录
/usr/sbin/nginx | 命令 | Nginx服务的启动管理的终端命令
/var/log/nginx | 目录 | Nginx的日志目录

3. 编译参数
- 略

#### Nginx变量
- HTTP请求变量 - `arg_PARAMETER、http_HEADER、send_http_HEADER`
    - 比如 `[User-Agent] $http_user_agent`
- 内置变量
- 自定义变量

#### Nginx模块
- Nginx官方模块
- Nginx第三方模块

#### Nginx
