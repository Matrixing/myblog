---
title: "开始写博客了"
date: 2026-11-15T02:45:00+08:00
draft: false
tags: ["Hugo", "Cloudflare"]
categories: ["Technology"]
summary: "用 Hugo + Cloudflare Pages 搭好了这个博客，记录一下第一篇和写作方式。"
---

这是第一篇文章，用来验证主题、代码高亮、目录和构建流程是否都正常。你可以直接删掉它，或者把它改造成自己的第一篇文章。

## 为什么要自己搭博客

平台写作省心，但内容、排版和访问速度都受制于人。静态站点只产出一堆 HTML，托管在 CDN 上，打开快、几乎不会挂、也几乎不花钱。

## 怎么写文章

### 新建

```bash
hugo new content posts/我的文章.md
```

新文件默认是 `draft: true`，写完把 `draft` 改成 `false` 再推送才会发布。

### 本地预览

```bash
hugo server -D
```

打开 <http://localhost:1313>，`-D` 表示连草稿一起渲染。改文件会实时刷新。

### 发布

```bash
git add -A && git commit -m "post: 我的文章" && git push
```

推送后 Cloudflare Pages 会自动构建，一分钟左右线上就能看到。

## 代码高亮效果

```go
package main

import "fmt"

func main() {
	fmt.Println("hello, hugo")
}
```

## 常用 front matter

| 字段 | 作用 |
|---|---|
| `title` | 标题 |
| `date` | 发布日期，决定排序 |
| `draft` | `true` 时只在 `-D` 预览，不会发布 |
| `tags` / `categories` | 标签与分类，自动生成归档页 |
| `summary` | 列表页摘要，不写则自动截取正文 |
| `cover.image` | 封面图，放在同目录下写相对路径 |

> 提示：标题层级从 `##` 开始写，目录（TOC）会自动抓取二级和三级标题。

## 接下来

- 把 `hugo.toml` 里的 `baseURL`、`title`、首页介绍改成自己的
- 建 GitHub 仓库并推送
- 在 Cloudflare Pages 里连上仓库，构建命令 `hugo --gc --minify`，输出目录 `public`
