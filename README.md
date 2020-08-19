# Quick start

```
将项目直接放置在当前目录下，添加对应的nginx配置文件至conf.d即可使用
```

```
配置环境变量

cp .env.example .env
```

```
docker-compose up 前台启动

docker-compose up -d 后台启动

docker-compose stop 暂停容器

docker-compose start 启动容器
```

```
进入容器

docker exec -it [container] sh
```

```
开启Xdebug

Preferences -> Set Debug port 9001

That's ok!
```
