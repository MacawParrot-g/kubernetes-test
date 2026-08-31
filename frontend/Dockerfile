# ==========================================
# 阶段 1: 使用 Node.js 进行打包构建
# ==========================================
FROM node:18-alpine AS build

# 设置工作目录
WORKDIR /app

# 复制依赖清单文件（利用 Docker 缓存，避免每次改代码都重装依赖）
COPY package.json package-lock.json ./

# 安装依赖（国内环境建议换成淘宝源加速，如果没限制可去掉 registry 配置）
RUN npm install --registry=https://registry.npmmirror.com 

# 复制前端源代码
COPY . .

# 执行打包构建 (根据你的项目框架修改)
# 如果是 Vue/React/Vite 项目，通常使用 npm run build
RUN npm run build

# ==========================================
# 阶段 2: 使用 Nginx 提供静态文件服务
# ==========================================
# 使用极简的 Nginx Alpine 镜像，减小体积
FROM nginx:stable-alpine

# 删除默认的 Nginx 配置文件
RUN rm /etc/nginx/conf.d/default.conf

# 复制我们定制的 Nginx 配置
COPY nginx.conf /etc/nginx/conf.d/default.conf

# 将第一阶段构建好的 dist 目录复制到 Nginx 的静态文件目录
# 注意：这里默认 dist 目录在 /app/dist，如果你的打包目录叫别的名字，请修改这里
COPY --from=build /app/dist /usr/share/nginx/html

# 暴露 Nginx 默认的 80 端口
EXPOSE 80

# 启动 Nginx 前台运行
CMD ["nginx", "-g", "daemon off;"]
