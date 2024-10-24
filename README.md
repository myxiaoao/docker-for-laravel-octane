# Docker for Laravel Octane (本地开发环境)

支持队列(horizon)、计划任务、缓存（Redis）、数据库（MySQL 或者 PostgresSQL）、Nginx 代理。

**本环境适用于本地开发。请勿在生产环境中使用！**

## 设置本地环境

1. 复制参数

```sh
复制 .env.example 对应的参数到 .env
```

2. 将当前用户身份数据添加到环境变量中

```sh
echo UID=$(id -u) >> .env
echo GID=$(id -g) >> .env
```

3. 运行 docker

```sh
docker compose up -d --build
```

4. 为包管理器生成缓存目录

```sh
docker compose exec -u root app install -o $(id -u) -g $(id -g) -d "/.npm" &&
docker compose exec -u root app install -o $(id -u) -g $(id -g) -d "/.composer"
```

5. 安装 composer 依赖

```sh
docker compose exec app composer install
```

6. 安装 npm 依赖

```sh
docker compose exec app npm i
```

7. 生成应用密钥

```sh
docker compose exec app php artisan key:generate
```

8. 如果服务不健康 - 重启 docker

```sh
docker compose up -d --build
```

### 访问入口

- [http://localhost/](http://localhost/) - application
- [http://localhost/horizon](http://localhost/horizon) - queue manager

## 说明

1. 使用 `postgresql` 环境为相对应的后缀配置文件。
2. `postgresql` 使用镜像为 `postgis` 默认带有 `PostGIS` 插件功能的镜像。
3. 镜像中 `composer` 和 `npm` 已重新设置源。

## 推荐

> 国内环境安装 docker : https://linuxmirrors.cn/other/
