# SRT Explorer 部署指南

## 项目信息

| 项目 | 信息 |
|------|------|
| **项目名称** | SRT Explorer 空间转录组数据库 |
| **GitHub 仓库** | https://github.com/PWO-CHINA/spatial-transcriptomics-browser |
| **技术栈** | React + TypeScript + Vite + Tailwind CSS |
| **创建日期** | 2025年11月29日 |

---

## 在线访问地址

| 平台 | 链接 | 适合用户 | 特点 |
|------|------|---------|------|
| **Vercel** | https://spatial-transcriptomics-browser.vercel.app | 海外用户 | 自动部署，100GB/月带宽 |
| **Cloudflare Pages** | https://spatial-transcriptomics-browser.pages.dev | 国内用户 | 国内访问更快，无限带宽 |

---

## 部署架构

```
GitHub 仓库 (main 分支)
       │
       ├──────────────────┬──────────────────┐
       ▼                  ▼                  
   Vercel            Cloudflare Pages       
   (自动部署)          (自动部署)             
       │                  │                  
       ▼                  ▼                  
 xxx.vercel.app    xxx.pages.dev           
   (海外访问)         (国内访问)              
```

---

## 更新部署

代码修改后，只需执行以下命令，两个平台会自动同步更新：

```powershell
cd "d:\the lab for react_web\paper_abstract_v1"
git add .
git commit -m "更新说明"
git push
```

---

## Vercel 配置

- **平台**: https://vercel.com
- **账号**: GitHub 登录 (PWO-CHINA)
- **项目**: spatial-transcriptomics-browser
- **构建设置**:
  - Framework Preset: Vite
  - Build Command: `npm run build`
  - Output Directory: `dist`
- **免费额度**:
  - 带宽: 100 GB/月
  - 构建时间: 6000 分钟/月
  - 部署次数: 无限制

---

## Cloudflare Pages 配置

- **平台**: https://dash.cloudflare.com → Workers & Pages
- **账号**: qianwenda2005@gmail.com
- **项目**: spatial-transcriptomics-browser
- **构建设置**:
  - Framework Preset: **None** (重要！不要选 Vite/VitePress)
  - Build Command: `npm run build`
  - Build Output Directory: `dist`
  - Deploy Command: **留空**
- **免费额度**:
  - 带宽: **无限制**
  - 请求数: **无限制**
  - 构建次数: 500 次/月

---

## 本地开发

```powershell
# 进入项目目录
cd "d:\the lab for react_web\paper_abstract_v1"

# 安装依赖
npm install

# 启动开发服务器
npm run dev

# 构建生产版本
npm run build

# 预览生产版本
npm run preview
```

---

## 项目结构

```
paper_abstract_v1/
├── src/
│   ├── App.tsx          # 主组件 (包含所有56个技术数据)
│   ├── main.tsx         # 入口文件
│   └── index.css        # Tailwind 样式
├── index.html           # HTML 模板
├── package.json         # 依赖配置
├── vite.config.ts       # Vite 配置
├── tailwind.config.js   # Tailwind 配置
├── tsconfig.json        # TypeScript 配置
└── .gitignore           # Git 忽略文件
```

---

## 常见问题

### 1. 部署失败：TypeScript 错误
**原因**: 有未使用的导入
**解决**: 删除未使用的 import，如 `React`, `FileText`, `Zap` 等

### 2. Cloudflare 构建失败：Output directory not found
**原因**: Framework preset 选错了
**解决**: 将 Framework preset 改为 **None**

### 3. Cloudflare 部署失败：wrangler 错误
**原因**: Deploy command 填了 wrangler 命令
**解决**: Deploy command 留空

### 4. 国内访问慢
**解决**: 使用 Cloudflare Pages 的链接 (xxx.pages.dev)

---

## VS Code 安全设置

为防止 Agent 误删文件，已配置以下保护：

```json
{
  "explorer.confirmDelete": true,
  "explorer.confirmDragAndDrop": true,
  "chat.editing.confirmEditRequestRemoval": true,
  "files.readonlyInclude": {
    "**/.git/**": true,
    "**/.gitignore": true,
    "**/package.json": true,
    "**/package-lock.json": true
  }
}
```

---

## 维护日志

| 日期 | 操作 | 备注 |
|------|------|------|
| 2025-11-29 | 项目创建 | React + Vite + Tailwind |
| 2025-11-29 | Vercel 部署 | 首次部署成功 |
| 2025-11-30 | Cloudflare 部署 | 国内访问优化 |
| 2025-11-30 | 添加 DOI 链接 | 参考文献链接 |

---

## 联系方式

- **GitHub**: https://github.com/PWO-CHINA
- **Email**: qianwenda2005@gmail.com
