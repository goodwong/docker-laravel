

# docker project for Laravel
> 只供开发使用
> 体积小，编译／下载速度快
> 针对中国网络环境加速（为中国加油！）


## 特色
1. 在一个docker环境下，运行一个命令 docker-compose up，即可开始一个完整的laravel开发环境
2. 可以支持多个项目同时运行（只要端口不冲突）


#### 速度优化：
- alpine使用国内镜像
- composer使用国内镜像
- npm使用国内镜像


#### 体积优化
- 全部使用alpine以及以此为基础的docker包
- 分层构建，重复利用文件层（如php依赖）


## 使用

1. 加入项目
```shell
git submodule add https://github.com/goodwong/docker-laravel.git .docker-compose
```

2. 配置`docker-compose/.env`文件
```shell
cd .docker-compose/ # 进入docker-laravel子目录（一般命名为 .docker-compose）
cp .env.example .env
```
并且修改里面的少量配置


3. 启动服务
```shell
cd .docker-compose/
docker-compose up -d
```


4. php操作（包含composer/artisan）
```shell
cd .docker-compose/
docker-compose exec workspace sh
```


5. 访问
- http://localhost:your-web-port （web）
- http://localhost:your-adminer-port/?pgsql=db&db=app （DB管理，初始用户名密码都是app）



## 新Laravel项目操作
> `workspace`容器里面

1. 配置`laravel`的.env文件
```ini
DB_CONNECTION=pgsql
DB_HOST=db
DB_PORT=5432
DB_DATABASE=app
DB_USERNAME=app
DB_PASSWORD=app
```

2. 在`workspace`里面执行
```shell
composer install
cp .env.example .env # 注意需要编辑参数
php artisan key:generate
php artisan migrate
php artisan storage:link
```


## 配置`.docker-compose/.env`文件

#### 项目名（多项目开发时必须修改）
- COMPOSE_PROJECT_NAME=XX_APP

#### web端口
- NGINX_HOST_HTTP_PORT=80
- NGINX_HOST_HTTPS_PORT=443


#### 数据库管理
- DB_ADMINER_PORT=8001
- DB_PASSWORD=app
- DB_USER=app
- DB_NAME=app


#### 数据库管理（临时使用）
```shell
docker-compose run --rm -p 8080:8080 adminer
```
访问 http://localhost:8080


## TODO:

#### 优化
- adminer是从php:7.2-alpine，可以写一个，镜像重用，节省65MB流量，但是维护复杂度尚待考虑
  (尝试失败，直接复制adminer的Dockerfile会从s3墙外下载，速度更慢)
  (后续可以尝试直接复制文件进入镜像来制作)

#### 功能
- php-fpm 需要添加一个 build-scripts目录。里面的文件会在build的时候依次执行，用于按转自定义组件（如GD）。这个文件夹不进入git代码库