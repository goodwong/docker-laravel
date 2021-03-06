
# docker 环境 for Laravel

> 只供开发使用

> 体积小，编译／下载速度快

> 针对中国网络环境加速（为中国加油！）


## 特色
1. 在一个docker环境下，运行一个命令 docker-compose up，即可开始一个完整的laravel开发环境
2. 可以支持多个项目同时运行（只要端口不冲突）


#### 速度优化：（电信20MB带宽，2分钟左右完成构建）
- alpine使用国内镜像
- composer使用国内镜像
- ~~npm使用国内镜像~~（前端项目另起一个项目 [goodwong/docker-webpack](https://github.com/goodwong/docker-webpack) ）


#### 体积优化（使用镜像控制在200MB左右，压缩传输体积还要更小）
- 全部使用alpine以及以此为基础的docker包
- 分层构建，重复利用文件层（如php依赖）

附：镜像体积

$ docker-compose images

Container       |  Repository    |     Tag    |    Image Id   |   Size  
----------------|----------------|------------|---------------|---------
app_adminer_1   |  adminer       |  latest    |  9786c2d422b6 |  62.6 MB
app_db_1        |  postgres      |  10-alpine |  e6c5e6a76255 |  36.4 MB
app_nginx_1     |  nginx         |  alpine    |  bb00c21b4edf |  16 MB  
app_php-fpm_1   |  app_php-fpm   |  latest    |  3896900d3ffa |  77.9 MB
app_workspace_1 |  app_workspace |  latest    |  b130c5af648a |  80.3 MB

$ docker system df

TYPE          | TOTAL | ACTIVE | SIZE    | RECLAIMABLE
--------------|-------|--------|---------|---------------
Images        | 10    | 7      | 258.1MB | 84.21MB (32%)
Containers    | 11    | 11     | 23.99kB | 0B (0%)
Local Volumes | 8     | 2      | 448.7MB | 332MB (73%)
Build Cache   |       |        | 0B      | 0B



## 使用

1. 运行命令安装
    ```shell
    git submodule add https://github.com/goodwong/docker-laravel.git .docker-compose
    # git submodule add git@github.com:goodwong/docker-laravel.git .docker-compose
    ```

2. 配置`docker-compose/.env`文件
    ```shell
    cd .docker-compose/ # 进入docker-laravel子目录（一般命名为 .docker-compose）
    cp .env.example .env
    ```
    > 若是多项目运行，需要修改`.env`文件相关配置


3. 启动服务
    ```shell
    cd .docker-compose/

    # 启动web（包含nginx、db、redis）服务
    docker-compose up -d web

    # 启动worker服务（需要安装laravel/horizon）
    docker-compose up -d worker

    # 启动cron服务（一般生产环境才需要）
    docker-compose up -d cron

    # 或者一键启动所有服务 adminer、web、worker、cron、workspace
    docker-compose up -d
    ```


4. 在`workspace`容器里操作php（包含composer、artisan，权限为www-data）
    ```shell
    cd .docker-compose/
    docker-compose run --rm workspace sh
    ```


5. 访问
    - http://localhost:your-web-port （web）
    - http://localhost:your-adminer-port/?pgsql=db&db=app （DB管理，初始用户名密码都是app）



## 新Laravel项目操作
> 需要进入`workspace`容器里面
> ```shell
> docker-compose run --rm workspace sh
> ```

1. 复制`.env文件`
    ```shell
    cp .env.example .env # 注意需要编辑参数
    ```

2. 配置`laravel`的.env文件以下相应项：
    ```ini
    DB_CONNECTION=pgsql
    DB_HOST=db
    DB_PORT=5432
    DB_DATABASE=app
    DB_USERNAME=app
    DB_PASSWORD=app

    CACHE_DRIVER=redis
    SESSION_DRIVER=redis
    QUEUE_DRIVER=redis

    REDIS_HOST=redis
    ```

3. 安装依赖  

    **Predis**  
    https://laravel.com/docs/master/redis  

    ```shell
    composer require predis/predis
    ```

    **队列**  
    队列依赖`laravel/horizon`  
    若需要使用队列机制，需安装此模块
    ```shell
    composer require laravel/horizon
    ```

4. 安装模块、初始化
    ```shell
    composer install
    php artisan key:generate
    php artisan migrate
    php artisan storage:link
    ```


## 配置`.docker-compose/.env`文件

- 项目名（多项目开发时必须修改）
    ```ini
    COMPOSE_PROJECT_NAME=XX_APP
    ```

- web端口
    ```ini
    NGINX_HOST_HTTP_PORT=80
    ```


- 数据库
    ```ini
    DB_ADMINER_PORT=8001
    DB_PASSWORD=app
    DB_USER=app
    DB_NAME=app
    DB_DIR=./db/data
    ```


## 数据库管理

1. 启动`adminer`服务
    ```shell
    docker-compose run --rm -p 8899:8080 adminer
    #                           ^
    #                        此8899为本机端口，可以自己定义
    ```
    > 安全起见，即用即关
2. 访问 http://localhost:8899 #   <------  此`8899`即刚才所定义的端口


## TODO:

- 优化  
    adminer是从php:7.2-alpine，可以写一个，镜像重用，节省65MB流量，但是维护复杂度尚待考虑
    (尝试失败，直接复制adminer的Dockerfile会从s3墙外下载，速度更慢)
    (后续可以尝试直接复制文件进入镜像来制作)

- 功能  
    php-fpm 需要添加一个 build-scripts目录。里面的文件会在build的时候依次执行，用于按转自定义组件（如GD）。这个文件夹不进入git代码库
