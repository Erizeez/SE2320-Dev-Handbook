# Docker一键搭建环境

为了方便统一环境和快速搭建环境，又或者方便最后Docker部署，我们推荐用Docker搭建部分开发环境，例如Mysql、MongoDB、Redis等。

此处使用Docker Compose一键部署开发环境，请提前安装好[Docker Desktop](https://www.docker.com/get-started/)(推荐)或Docker Engine，安装后确保你可以在命令行中运行docker-compose。

运行指令如下：

| 指令                 | 意义                     |
| -------------------- | ------------------------ |
| docker-compose up    | 构建启动(推荐)           |
| docker-compose down  | 停止并删除容器           |
| docker-compose start | 启动                     |
| docker-compose stop  | 停止(此时并不会删除容器) |
| docker-compose rm    | 删除                     |

由于配置了volume以持久化数据，无需担心容器被删除导致数据丢失。

此处提供了Docker Compose[配置范本](./docker-compose.yml)。

下面将稍作说明。

```yaml
# ./docker-compose.yml
# 固定的文件名，不要修改
# Docker Compose配置文件版本号，不要修改
version: '3'

# 在服务下修改你需要的环境
services:
  mysql:
  	# 设置镜像
    image: mysql:latest
    # 设置环境变量
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - TZ=Asia/Shanghai
    # 设置端口映射，宿主机:容器内部
    ports:
      - "3306:3306"
    # 设置映射的文件夹
    # volume(声明于最底下)/文件路径:容器内部路径
    volumes:
      - mysql-data:/var/lib/mysql
      # 数据库还原目录 可将需要还原的sql文件放在这里
      - mysql-init:/docker-entrypoint-initdb.d
  mongodb:
    image: mongo:latest
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=admin
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db
  redis:
    image: redis:latest
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data

# 声明持久卷
volumes:
  # 卷名(被上面引用)
  mysql-data:
    driver: local
    driver_opts:
      type: none
      o: bind
      # 目录路径
      device: F:\DockerData\mysql-data
  mysql-init:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: F:\DockerData\mysql-init
  mongo-data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: F:\DockerData\mongo-data
  redis-data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: F:\DockerData\redis-data
```

