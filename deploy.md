# 一键部署到 Cloudflare Pages

## 前置要求

1. 安装 Node.js (建议 v18+)
2. 安装 Wrangler CLI: `npm install -g wrangler`
3. 登录 Cloudflare: `wrangler login`

## 一键部署脚本

### Windows (PowerShell)

```powershell
# 保存为 deploy.ps1，双击即可运行

$ErrorActionPreference = "Stop"

Write-Host "🚀 开始部署到 Cloudflare Pages..." -ForegroundColor Green

# 检查 wrangler 是否安装
if (-not (Get-Command wrangler -ErrorAction SilentlyContinue)) {
    Write-Host "❌ Wrangler CLI 未安装，正在安装..." -ForegroundColor Red
    npm install -g wrangler
}

# 项目目录
$PROJECT_DIR = Split-Path -Parent $MyInvocation.MyCommand.Path

# 部署命令
Write-Host "📦 正在上传文件..." -ForegroundColor Yellow
wrangler pages deploy "$PROJECT_DIR" --project-name=your-project-name

if ($LASTEXITCODE -eq 0) {
    Write-Host "✅ 部署成功！" -ForegroundColor Green
} else {
    Write-Host "❌ 部署失败！" -ForegroundColor Red
}
```

### Linux/Mac (Bash)

```bash
#!/bin/bash

set -e

echo "🚀 开始部署到 Cloudflare Pages..."

# 检查 wrangler 是否安装
if ! command -v wrangler &> /dev/null; then
    echo "❌ Wrangler CLI 未安装，正在安装..."
    npm install -g wrangler
fi

# 获取脚本所在目录
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

# 部署命令
echo "📦 正在上传文件..."
wrangler pages deploy "$SCRIPT_DIR" --project-name=your-project-name

echo "✅ 部署成功！"
```

## 使用方法

1. 将上述脚本保存为 `deploy.ps1` 或 `deploy.sh`
2. 修改 `--project-name=your-project-name` 为你的 Cloudflare Pages 项目名称
3. 双击运行或使用终端执行

## GitHub Actions 自动部署 (可选)

如果你使用 GitHub 仓库，可以创建 `.github/workflows/deploy.yml`:

```yaml
name: Deploy to Cloudflare Pages

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
      - run: npm install -g wrangler
      - run: wrangler pages deploy . --project-name=your-project-name
        env:
          CLOUDFLARE_API_TOKEN: ${{ secrets.CLOUDFLARE_API_TOKEN }}
```

然后在 Cloudflare Dashboard 创建 API Token，添加到 GitHub Secrets。
