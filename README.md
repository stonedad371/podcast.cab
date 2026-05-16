# podcast.cab 介绍页

纯静态单页，零依赖。可部署到任何静态托管。

## 结构

```
podcast-cab-landing/
├── index.html
├── style.css
└── assets/
    ├── poster.jpg        # OG image + 视频 poster
    ├── demo-30s.mp4      # 30 秒高光预览（hero 自动循环）
    └── demo-full.mp4     # 完整 4 分钟示例
```

总大小约 13 MB（视频占大头）。

## 本地预览

```bash
cd podcast-cab-landing
python3 -m http.server 8000
# 浏览器开 http://localhost:8000
```

或者直接 `open index.html`（但视频可能因 file:// 协议加载有问题）。

## 部署

### Cloudflare Pages（推荐 · 免费 · 国内可访问）

1. 把目录 push 到 GitHub repo（私有/公开都行）
2. Cloudflare Dashboard → Pages → 连接 GitHub repo
3. Build settings：
   - Build command: 留空
   - Output directory: `/`（或 `podcast-cab-landing` 如果是子目录）
4. 部署完拿到 `*.pages.dev` 域名
5. 在 podcast.cab DNS 加 CNAME → `*.pages.dev`

### Vercel（最简单）

```bash
npx vercel --prod
```

按提示登录、选项目即可。第一次会让你选 framework，选「Other」。Vercel 自动给你 `*.vercel.app`，再去 Vercel 后台绑 `podcast.cab` 自定义域名。

### Netlify

拖整个目录到 https://app.netlify.com/drop —— 立即拿到一个 `*.netlify.app`，然后绑定 podcast.cab。

### GitHub Pages

1. push 到 `<user>/podcast-cab` 仓库
2. Settings → Pages → Source 选 `main / root`
3. 在 podcast.cab DNS 设 CNAME → `<user>.github.io`
4. 仓库根目录加 `CNAME` 文件，内容 `podcast.cab`

### 自己服务器（nginx）

```nginx
server {
  listen 80;
  server_name podcast.cab;
  root /var/www/podcast-cab-landing;
  index index.html;

  location / {
    try_files $uri $uri/ /index.html;
  }
  location /assets/ {
    expires 30d;
    add_header Cache-Control "public, immutable";
  }
}
```

## 改链接前要做的

- `GitHub ↗` 导航链接、`从 GitHub 下载` CTA 按钮、`<code>git clone &lt;repo&gt;...` 文案里都是 `https://github.com/`——换成你的 podcast-video-app 仓库地址
- footer 里"开源代码" 同理
- 真正发布到 podcast.cab 前，把 OG 图换成更好看的（现在用的是 demo 首帧）

## 视频素材怎么再压

```bash
# 30 秒版本想再小一点（默认 1.7 MB）
ffmpeg -ss 5 -t 30 -i source.mp4 -vf scale=540:960 -crf 28 -movflags +faststart demo-30s.mp4

# 完整版（默认 11 MB）
ffmpeg -i source.mp4 -vf scale=720:1280 -crf 30 -preset slow -movflags +faststart demo-full.mp4
```

## 改样式

所有颜色用 CSS 变量在 `:root` 里定义，改 `--accent` 就能换主题色。响应式断点 `@media (max-width: 900px)` 在 style.css 末尾。
