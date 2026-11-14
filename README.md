# myblog

Hugo + PaperMod 的静态博客，部署在 Cloudflare Pages。

## 目录结构

```
myblog/
├── hugo.toml                  # 全站配置（标题、菜单、主题参数）
├── content/
│   ├── about.md               # 「关于」页面
│   └── posts/
│       ├── _index.md          # 文章列表页
│       └── hello-world.md     # 示例文章，可删
├── themes/PaperMod/           # 主题（已随仓库提交，不依赖 submodule）
├── archetypes/default.md      # 新建文章时的模板
└── .gitignore
```

## 本地开发

```bash
hugo server -D          # http://localhost:1313，-D 表示包含草稿
hugo --gc --minify      # 生产构建，输出到 public/
```

本机 Hugo 版本：`v0.162.1+extended`（PaperMod 要求 ≥ 0.158）。

## 写文章

```bash
hugo new content posts/我的文章.md
```

1. 编辑刚生成的文件，把 front matter 里的 `draft: true` 改成 `false`
2. `hugo server -D` 预览
3. `git add -A && git commit -m "post: 我的文章" && git push`

推送后 Cloudflare Pages 自动构建发布。

## 部署到 Cloudflare Pages

1. 在 GitHub 上新建仓库（**不要**勾选 README/.gitignore），然后本地：

   ```bash
   git remote add origin git@github.com:你的用户名/myblog.git
   git push -u origin main
   ```

2. Cloudflare Dashboard → **Workers & Pages** → **Create application** → **Pages** → **Connect to Git**，选中刚才的仓库。
3. 构建设置：

   | 配置项 | 值 |
   | --- | --- |
   | Framework preset | Hugo |
   | Build command | `hugo --gc --minify` |
   | Build output directory | `public` |
   | Root directory | `/`（留空） |

4. 环境变量（**必填**，否则用的是镜像里很旧的 Hugo）：

   | 变量 | 值 |
   | --- | --- |
   | `HUGO_VERSION` | `0.162.1` |
   | `HUGO_ENV` | `production` |
   | `TZ` | `Asia/Shanghai` |

5. Save and Deploy，成功后得到 `https://<项目名>.pages.dev`。
6. 绑定自定义域名：项目 → **Custom domains** → 添加域名；域名在 Cloudflare 上会自动建 DNS 记录，否则去域名商加 `CNAME` 指向 `<项目名>.pages.dev`。
7. 绑定域名后，把 `hugo.toml` 的 `baseURL` 改成 `https://你的域名/`，再推一次。

## 常见问题

| 现象 | 处理 |
| --- | --- |
| 构建报 `TOCSS: ... not available in your current Hugo version` | `HUGO_VERSION` 没设或版本太低，改成 `0.162.1` |
| 构建报 `unexpected EOF when executing "sass"` | Hugo 升到了 0.170+（取消了 extended 版，改用 Dart Sass）。退回 `0.162.1`，或本地 `brew unpin hugo` 时同步改云端版本 |
| 线上样式丢失 | 检查 `themes/PaperMod` 是否已提交进仓库 |
| 改了页面没更新 | 看 Pages 的 Deployments，确认推送的是 `main` 分支 |

## 可选增强

- **评论**：在 [giscus.app](https://giscus.app) 用 GitHub 仓库开启 Discussions，拿到配置后填入 `hugo.toml` 的 `[params.giscus]`，并把 `comments = true`。
- **访问统计**：Cloudflare Dashboard → Web Analytics → 添加站点，拿到 JS 片段后放进 `layouts/partials/extend_head.html`（新建该文件）。
- **搜索**：已启用（`[outputs] home` 里的 `JSON`），点右上角搜索图标即可。

## 更新主题

主题是直接提交进仓库的（vendored），没有用 submodule。更新时：

```bash
curl -sL https://codeload.github.com/adityatelange/hugo-PaperMod/tar.gz/refs/heads/master -o /tmp/pm.tgz
tar xzf /tmp/pm.tgz -C /tmp && rm -rf themes/PaperMod && mv /tmp/hugo-PaperMod-master themes/PaperMod
hugo --gc --minify   # 确认没报错再提交
```
