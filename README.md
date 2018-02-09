
# docker project for Laravel
> 只供开发使用
> 体积小，编译／下载速度快
> 针对中国网络环境加速（为中国加油！）


### 特色
1. 在一个docker环境下，运行一个命令 docker-compose up，即可开始一个完整的laravel开发环境
2. 可以支持多个项目同时运行（只要端口不冲突）


### 速度优化：
- alpine使用国内镜像
- composer使用国内镜像
- npm使用国内镜像


### 体积优化
- 全部使用alpine以及以此为基础的docker包
- 分层构建，重复利用文件层（如php依赖）


## 使用


### 配置文件
```shell
cp .env.example .env
```
并且修改里面的少量配置


### 启动服务
```shell
docker-compose up -d
```


### php操作（包含composer/artisan）
```shell
docker-compose exec workspace sh
```


### 访问
- http://localhost:80 （web）
- http://localhost:8001/?pgsql=db&db=app （DB管理，初始用户名密码都是app）


## 配置.env文件

### web端口
- NGINX_HOST_HTTP_PORT=80
- NGINX_HOST_HTTPS_PORT=443


### 数据库管理
- DB_ADMINER_PORT=8001
- DB_PASSWORD=app
- DB_USER=app
- DB_NAME=app


### 数据库管理（临时使用）
```shell
docker-compose run --rm -p 8080:8080 adminer
```
访问 http://localhost:8080


## TODO:

### 优化
- adminer是从php:7.2-alpine，可以写一个，镜像重用，节省65MB流量，但是维护复杂度尚待考虑
  (尝试失败，直接复制adminer的Dockerfile会从s3墙外下载，速度更慢)
  (后续可以尝试直接复制文件进入镜像来制作)

### 功能
- php-fpm 需要添加一个 build-scripts目录。里面的文件会在build的时候依次执行，用于按转自定义组件（如GD）。这个文件夹不进入git代码库