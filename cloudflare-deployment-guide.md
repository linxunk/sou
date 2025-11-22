# Cloudflare Pages 部署指南

## 📋 项目准备

### 1. 项目优化

**现有项目信息：**
- 项目类型：React + Vite + TypeScript
- 构建命令：`npm run build`
- 输出目录：`dist`
- Node版本：推荐 18.x

### 2. 需要的配置文件

**创建 `_headers` 文件：**
```
/*
  X-Frame-Options: DENY
  X-XSS-Protection: 1; mode=block
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin

/*.html
  Cache-Control: public, max-age=31536000, immutable

/assets/*
  Cache-Control: public, max-age=31536000, immutable
```

**创建 `wrangler.toml` 文件：**
```toml
name = "search-system"
compatibility_date = "2024-01-01"

[site]
bucket = "./dist"

[build]
command = "npm run build"
cwd = "."

[build.environment]
NODE_VERSION = "18"
```

## 🚀 Cloudflare Pages 部署步骤

### 方法一：通过 Cloudflare Dashboard（推荐）

1. **登录 Cloudflare**
   - 访问 https://dash.cloudflare.com
   - 登录您的账户

2. **创建 Pages 项目**
   - 点击左侧菜单 "Pages"
   - 点击 "创建应用程序"
   - 选择 "连接到 Git"

3. **连接 Git 仓库**
   - 选择您的 Git 提供商（GitHub/GitLab）
   - 授权 Cloudflare 访问
   - 选择您的搜索系统仓库

4. **配置构建设置**
   ```
   构建命令：npm run build
   构建输出目录：dist
   Node.js 版本：18
   ```

5. **环境变量设置**
   在 "环境变量" 部分添加：
   ```
   NODE_VERSION = 18
   NPM_FLAGS = --prefer-offline
   ```

6. **部署项目**
   - 点击 "保存并部署"
   - 等待构建完成（通常 2-5 分钟）

### 方法二：通过 Wrangler CLI

1. **安装 Wrangler**
   ```bash
   npm install -g wrangler
   ```

2. **登录 Cloudflare**
   ```bash
   wrangler login
   ```

3. **部署项目**
   ```bash
   wrangler pages deploy ./dist --project-name=search-system
   ```

## 🔧 项目调整

### 更新 package.json
确保 `package.json` 中的构建脚本正确：

```json
{
  "scripts": {
    "build": "npm run build",
    "build:prod": "NODE_ENV=production npm run build"
  }
}
```

### 创建部署脚本
**创建 `deploy.sh`：**
```bash
#!/bin/bash
echo "开始构建项目..."
npm run build
echo "构建完成，开始部署到 Cloudflare..."
wrangler pages deploy ./dist --project-name=search-system --branch=main
echo "部署完成！"
```

## 🌐 域名配置

### 1. 自定义域名
- 在 Cloudflare Pages 项目设置中
- 点击 "自定义域名"
- 添加您的域名（如：search.yourdomain.com）
- 按照指示配置 DNS 记录

### 2. DNS 配置
在 Cloudflare DNS 中添加：
```
类型: CNAME
名称: search
内容: search-system.pages.dev
```

## 📊 环境变量配置

在 Cloudflare Pages 项目中设置以下环境变量：

**生产环境：**
```
NODE_VERSION=18
NPM_FLAGS=--prefer-offline
```

**预览环境：**
```
NODE_VERSION=18
NPM_FLAGS=--prefer-offline
```

## 🔒 安全配置

### 1. 密码保护
当前管理员密码 `20160607yY!` 存储在代码中，建议：
- 使用环境变量存储敏感信息
- 考虑使用 Cloudflare Access 进行额外保护

### 2. HTTPS
Cloudflare Pages 自动提供免费 HTTPS 证书

## 📈 性能优化

### 1. 缓存配置
已创建 `_headers` 文件配置适当的缓存策略

### 2. 压缩
Cloudflare 自动启用 Gzip/Brotli 压缩

## 🐛 常见问题

### 构建失败
- 检查 Node.js 版本设置
- 确保所有依赖在 `package.json` 中
- 验证构建命令正确

### 页面刷新 404
添加 `_redirects` 文件：
```
/*    /index.html   200
```

### 路由问题
对于 React Router，需要配置服务器重定向规则

## 📝 监控和维护

### 1. 构建日志
在 Cloudflare Dashboard 中查看详细构建日志

### 2. 访问统计
Cloudflare Analytics 提供免费流量分析

### 3. 错误监控
利用 Cloudflare 的错误页面和分析工具

## 🎯 部署完成后的检查清单

- [ ] 网站正常访问
- [ ] 搜索功能工作正常
- [ ] 管理员登录可用
- [ ] 响应式设计适配
- [ ] HTTPS 证书生效
- [ ] 域名解析正确
- [ ] 构建日志无错误

---

**项目地址示例：** https://search-system.pages.dev 或您设置的自定义域名