# Docker 部署指南

本项目支持使用 Docker 进行快速部署，包含开发环境和生产环境的配置。

## 前置要求

- Docker >= 20.10
- Docker Compose >= 2.0

## 快速开始

### 开发环境部署

```bash
# 启动开发环境
docker-compose --profile dev up -d

# 查看日志
docker-compose --profile dev logs -f app-dev

# 停止开发环境
docker-compose --profile dev down
```

开发环境将在 http://localhost:3000 运行，支持热重载。

### 生产环境部署

```bash
# 构建并启动生产环境
docker-compose --profile prod up -d

# 查看日志
docker-compose --profile prod logs -f app-prod

# 停止生产环境
docker-compose --profile prod down
```

生产环境将在 http://localhost (端口80) 运行。

## 详细配置

### 单独使用 Dockerfile

#### 开发环境
```bash
# 构建开发镜像
docker build --target development -t 91writing:dev .

# 运行开发容器
docker run -d -p 3000:3000 -v $(pwd):/app -v /app/node_modules --name 91writing-dev 91writing:dev
```

#### 生产环境
```bash
# 构建生产镜像
docker build --target production -t 91writing:prod .

# 运行生产容器
docker run -d -p 80:80 --name 91writing-prod 91writing:prod
```

## 故障排查

### 常见问题

1. 端口被占用 - 修改 docker-compose.yml 中的端口映射
2. 构建失败 - 使用 docker builder prune 清理缓存
3. 容器无法启动 - 查看 docker-compose logs 获取错误信息 