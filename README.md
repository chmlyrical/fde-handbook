# FDE 8 周面试冲刺 · 学习手册静态站点

纯静态 HTML 站点（无后端、无构建步骤），可直接部署到 GitHub Pages / Cloudflare Pages / 任意静态托管。

## 目录结构
- `index.html` —— 入口，自动跳转到 `FDE-WXDX.html`（学习手册总览）
- `FDE-WXDX.html` —— 左侧按 W1–W8 分组的 56 天导航，iframe 内联加载手册
- `学习手册首页.html` —— 门户首页（按周列出全部手册 + 评测题）
- `FDE-W*D*-学习手册.html` —— 56 天面试级学习手册
- `FDE-*-评测题.md` —— 对应天的自测题

所有引用均为相对路径，可在子路径（如 `/model`）下直接运行。

## 部署到 GitHub Pages（两步）

1. 在 GitHub 新建公开仓库，命名为 `model`（仓库名决定子路径）。
2. 在本目录执行：
   ```bash
   git init
   git add -A
   git commit -m "FDE 学习手册静态站点"
   git branch -M main
   git remote add origin git@github.com:<你的用户名>/model.git
   git push -u origin main
   ```
3. 仓库 Settings → Pages → Source 选 `Deploy from a branch` → `main` / `(root)` → Save。
   几分钟后站点出现在 `https://<你的用户名>.github.io/model/`。

## 绑定自己的域名 piccc.cc/model

⚠️ 注意：你现有 `piccc.cc` 已在提供服务，**不要**把 `piccc.cc` 的 DNS A 记录直接指向 GitHub（会接管整个根域，搞挂现有服务）。

### 方案 A（推荐，得到 piccc.cc/model，不动现有服务）
- GitHub 仓库**不设置**自定义域名（保持 `username.github.io/model/`）。
- 进 Cloudflare（piccc.cc 的 DNS 在这里）→ Rules → Transform Rules → 新建 **Rewrite**：
  - 条件：主机名 = `piccc.cc` 且 路径 匹配 `^/model/(.*)$`
  - 操作：重写到 `username.github.io`，路径改为 `/model/${1}`
  - 再补一条：路径 = `/model` 时重定向/重写到 `/model/`（补尾部斜杠）
- 这样 `piccc.cc/model` 由 CF 反向代理到 GitHub Pages，apex 服务不受影响。

### 方案 B（最简单，但 URL 变成 model.piccc.cc）
- GitHub Pages 自定义域名填 `model.piccc.cc`，并在仓库放一个 `CNAME` 文件，内容 `model.piccc.cc`。
- Cloudflare 给 `model.piccc.cc` 加一条 CNAME 指向 `username.github.io`。

## 本地预览
```bash
python3 -m http.server 8000   # 然后访问 http://localhost:8000/
```
