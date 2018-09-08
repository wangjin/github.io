---
title: Docker Compose学习笔记
date: 2018-09-08 14:16:36
tags:
 - Docker
 - Docker Compose
---

# 简介
`Docker Compose`是一个用于定义和运行多容器Docker应用程序的工具。通过使用YAML文件来配置应用程序的服务，并使用`docker-compose`相关命令从配置中创建并启动所有服务。

使用Compose通常分为以下三部：
1. 使用`Dockerfile`定义应用
2. 使用`docker-compose.yml`定义服务
3. 使用`docker-compose up`构建并启动应用

`docker-compose.yml`配置文件一般长这样：
```xml
version: '3'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

# 安装Docker Compose（Linux）
## 安装
1. 运行下面的命令下载最新的Docker Compose
 ```bash
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
 ```
2. 为`docker-compose`命令添加可执行权限
 ```bash
$ sudo chmod +x /usr/local/bin/docker-compose
 ```

3. 测试安装是否正确
```bash
$ docker-compose --version
```
## 卸载
```bash
$ sudo rm /usr/local/bin/docker-compose
```

# 官方Start Up

## 步骤一：设置
1. 创建一个测试目录
```bash
$ mkdir composetest
$ cd composetest
```

2. 创建一个名为`app.py`的文件：
```python
import time

import redis
from flask import Flask


app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)


def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)


@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

上述例子中的`redis`是redis容器的在应用所属网络上的hostname，多个docker应用处于相同网络下时，通过hostname即可互相访问。

3. 创建另一个名为`requirements.txt`的文件并添加以下内容：
```txt
flask
redis
```

## 步骤二：创建Dockerfile
在这个步骤中，创建一个Dockerfile来构建Docker镜像。这个镜像包含Python环境和Python应用所需的所有依赖。

在项目目录中创建一个名为`Dockerfile`的文件并添加以下内容：
```Dockerfile
FROM python:3.4-alpine
ADD . /code
WORKDIR /code
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

Dockerfile构建为镜像步骤：

- 以Python 3.4-alpine镜像为基础构建一个镜像
- 把当前目录`.`下的所有文件添加到镜像的`/code`目录中
- 设置工作目录为`/code`
- 运行安装Python依赖的命令
- 设置容器默认启动命令为`python app.py`

## 步骤三：在Compose文件中定义服务

在项目目录中创建一个名为`docker-compose.yml`的文件并添加以下内容：

```yaml
version: '3'
services:
  web:
    build: .
    ports:
     - "5000:5000"
  redis:
    image: "redis:alpine"
```

上面这个Compose文件定义了两个服务，`web`和`redis`。

`web`服务：

- 使用当前目录下的`Dockerfile`所构建的镜像
- 在容器和宿主机中暴露`5000`端口，这是Flask web的默认端口

`redis`服务使用Docker Hub registry中的镜像构建。

## 步骤四：使用Compose构建和运行应用

1. 在工程目录下执行`docker-compose`命令：

   ```bash
   $ docker-compose up
   Building web
   Step 1/5 : FROM python:3.4-alpine
    ---> 73266cf72156
   Step 2/5 : ADD . /code
    ---> 93d41f61c2f8
   Step 3/5 : WORKDIR /code
    ---> Running in 74e35fbb2368
   Removing intermediate container 74e35fbb2368
    ---> 2dc16f910cce
   Step 4/5 : RUN pip install -r requirements.txt
    ---> Running in c82ef26c57ea
   Collecting flask (from -r requirements.txt (line 1))
     Downloading https://files.pythonhosted.org/packages/7f/e7/08578774ed4536d3242b14dacb4696386634607af824ea997202cd0edb4b/Flask-1.0.2-py2.py3-none-any.whl (91kB)
   Collecting redis (from -r requirements.txt (line 2))
     Downloading https://files.pythonhosted.org/packages/3b/f6/7a76333cf0b9251ecf49efff635015171843d9b977e4ffcf59f9c4428052/redis-2.10.6-py2.py3-none-any.whl (64kB)
   Collecting Jinja2>=2.10 (from flask->-r requirements.txt (line 1))
     Downloading https://files.pythonhosted.org/packages/7f/ff/ae64bacdfc95f27a016a7bed8e8686763ba4d277a78ca76f32659220a731/Jinja2-2.10-py2.py3-none-any.whl (126kB)
   Collecting click>=5.1 (from flask->-r requirements.txt (line 1))
     Downloading https://files.pythonhosted.org/packages/34/c1/8806f99713ddb993c5366c362b2f908f18269f8d792aff1abfd700775a77/click-6.7-py2.py3-none-any.whl (71kB)
   Collecting Werkzeug>=0.14 (from flask->-r requirements.txt (line 1))
     Downloading https://files.pythonhosted.org/packages/20/c4/12e3e56473e52375aa29c4764e70d1b8f3efa6682bef8d0aae04fe335243/Werkzeug-0.14.1-py2.py3-none-any.whl (322kB)
   Collecting itsdangerous>=0.24 (from flask->-r requirements.txt (line 1))
     Downloading https://files.pythonhosted.org/packages/dc/b4/a60bcdba945c00f6d608d8975131ab3f25b22f2bcfe1dab221165194b2d4/itsdangerous-0.24.tar.gz (46kB)
   Collecting MarkupSafe>=0.23 (from Jinja2>=2.10->flask->-r requirements.txt (line 1))
     Downloading https://files.pythonhosted.org/packages/4d/de/32d741db316d8fdb7680822dd37001ef7a448255de9699ab4bfcbdf4172b/MarkupSafe-1.0.tar.gz
   Building wheels for collected packages: itsdangerous, MarkupSafe
     Running setup.py bdist_wheel for itsdangerous: started
     Running setup.py bdist_wheel for itsdangerous: finished with status 'done'
     Stored in directory: /root/.cache/pip/wheels/2c/4a/61/5599631c1554768c6290b08c02c72d7317910374ca602ff1e5
     Running setup.py bdist_wheel for MarkupSafe: started
     Running setup.py bdist_wheel for MarkupSafe: finished with status 'done'
     Stored in directory: /root/.cache/pip/wheels/33/56/20/ebe49a5c612fffe1c5a632146b16596f9e64676768661e4e46
   Successfully built itsdangerous MarkupSafe
   Installing collected packages: MarkupSafe, Jinja2, click, Werkzeug, itsdangerous, flask, redis
   Successfully installed Jinja2-2.10 MarkupSafe-1.0 Werkzeug-0.14.1 click-6.7 flask-1.0.2 itsdangerous-0.24 redis-2.10.6
   Removing intermediate container c82ef26c57ea
    ---> b7c7a9180594
   Step 5/5 : CMD ["python", "app.py"]
    ---> Running in 874657b8b1c8
   Removing intermediate container 874657b8b1c8
    ---> 3398cf709b5c
   Successfully built 3398cf709b5c
   Successfully tagged composetest_web:latest
   WARNING: Image for service web was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
   composetest_redis_1 is up-to-date
   Creating composetest_web_1 ... done
   Attaching to composetest_redis_1, composetest_web_1
   redis_1  | 1:C 08 Sep 07:35:46.399 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
   redis_1  | 1:C 08 Sep 07:35:46.400 # Redis version=4.0.11, bits=64, commit=00000000, modified=0, pid=1, just started
   redis_1  | 1:C 08 Sep 07:35:46.400 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
   redis_1  | 1:M 08 Sep 07:35:46.405 * Running mode=standalone, port=6379.
   redis_1  | 1:M 08 Sep 07:35:46.405 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
   redis_1  | 1:M 08 Sep 07:35:46.405 # Server initialized
   redis_1  | 1:M 08 Sep 07:35:46.406 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
   redis_1  | 1:M 08 Sep 07:35:46.406 * Ready to accept connections
   redis_1  | 1:signal-handler (1536392231) Received SIGTERM scheduling shutdown...
   redis_1  | 1:M 08 Sep 07:37:11.181 # User requested shutdown...
   redis_1  | 1:M 08 Sep 07:37:11.182 * Saving the final RDB snapshot before exiting.
   redis_1  | 1:M 08 Sep 07:37:11.188 * DB saved on disk
   redis_1  | 1:M 08 Sep 07:37:11.188 # Redis is now ready to exit, bye bye...
   redis_1  | 1:C 08 Sep 07:37:14.802 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
   redis_1  | 1:C 08 Sep 07:37:14.802 # Redis version=4.0.11, bits=64, commit=00000000, modified=0, pid=1, just started
   redis_1  | 1:C 08 Sep 07:37:14.802 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
   redis_1  | 1:M 08 Sep 07:37:14.803 * Running mode=standalone, port=6379.
   redis_1  | 1:M 08 Sep 07:37:14.804 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
   redis_1  | 1:M 08 Sep 07:37:14.804 # Server initialized
   redis_1  | 1:M 08 Sep 07:37:14.804 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
   redis_1  | 1:M 08 Sep 07:37:14.804 * DB loaded from disk: 0.000 seconds
   redis_1  | 1:M 08 Sep 07:37:14.804 * Ready to accept connections
   web_1    |  * Serving Flask app "app" (lazy loading)
   web_1    |  * Environment: production
   web_1    |    WARNING: Do not use the development server in a production environment.
   web_1    |    Use a production WSGI server instead.
   web_1    |  * Debug mode: on
   web_1    |  * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
   web_1    |  * Restarting with stat
   web_1    |  * Debugger is active!
   web_1    |  * Debugger PIN: 303-324-532
   ```

2. 在浏览器输入`http://localhost:5000/`查看应用是否运行正常。

3. 在终端中输入`docker images ls`查看本地镜像：

   ```bash
   $ docker image ls
   REPOSITORY                                 TAG                 IMAGE ID            CREATED             SIZE
   composetest_web                            latest              3398cf709b5c        5 minutes ago       78.4MB
   redis                                      alpine              162794862d37        12 hours ago        30MB
   python                                     3.4-alpine          73266cf72156        3 days ago          67.1MB
   ```

## 步骤五：修改Compose文件添加目录挂载

修改`docker-compose.yml`，给`web`服务添加一个目录挂载

```yaml
version: '3'
services:
  web:
    build: .
    ports:
     - "5000:5000"
    volumes:
     - .:/code
  redis:
    image: "redis:alpine"
```

`volumes`键值把当前目录挂载到容器内`/code`目录

## 步骤六：使用Compose重新构建和运行应用

```bash
$ docker-compose up
Creating network "composetest_default" with the default driver
Creating composetest_web_1   ... done
Creating composetest_redis_1 ... done
Attaching to composetest_redis_1, composetest_web_1
redis_1  | 1:C 08 Sep 08:00:35.940 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis_1  | 1:C 08 Sep 08:00:35.940 # Redis version=4.0.11, bits=64, commit=00000000, modified=0, pid=1, just started
redis_1  | 1:C 08 Sep 08:00:35.940 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
redis_1  | 1:M 08 Sep 08:00:35.941 * Running mode=standalone, port=6379.
redis_1  | 1:M 08 Sep 08:00:35.941 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
redis_1  | 1:M 08 Sep 08:00:35.941 # Server initialized
redis_1  | 1:M 08 Sep 08:00:35.941 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
redis_1  | 1:M 08 Sep 08:00:35.941 * Ready to accept connections
web_1    |  * Serving Flask app "app" (lazy loading)
web_1    |  * Environment: production
web_1    |    WARNING: Do not use the development server in a production environment.
web_1    |    Use a production WSGI server instead.
web_1    |  * Debug mode: on
web_1    |  * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
web_1    |  * Restarting with stat
web_1    |  * Debugger is active!
web_1    |  * Debugger PIN: 741-610-125
```

