+++
title = "为 Hugo 网站添加 Web 端博客编辑器"
description = "为 Hugo 网站添加 Web 端博客编辑器 - Lens Note"
date = "2026-04-23"
draft = false
categories = ["前端"]
+++

# 如何为 Hugo 网站添加 Web 端博客编辑器

最近维护我的个人博客时，发现每次写新文章都要在本地编辑 Markdown 文件再推送到 GitHub，这个过程比较繁琐。于是我想：能不能直接在浏览器里写博客？

## 技术选型

经过一番调研，我发现有几种方案：

### 方案一：Decap CMS（原 Netlify CMS）
- 优点：功能完善，支持富文本编辑器
- 缺点：需要配置 OAuth 认证，对 Vercel 支持不够友好

### 方案二：GitHub 网页端直接编辑
- 优点：无需额外配置
- 缺点：体验较差，需要懂文件路径和 Front Matter 格式

### 方案三：自建轻量级编辑器
- 优点：完全可控，无需后端服务
- 缺点：需要自己编写前端代码

## 核心实现

### 1. 文件结构

### 2. 关键技术点

**调用 GitHub API 创建文件：**

```javascript
const res = await fetch(
  `https://api.github.com/repos/${owner}/${repo}/contents/{path}`,
  {
    method: 'PUT',
    headers: {
      'Authorization': `token {token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      message: 'post: add new article',
      content: base64Content,
      branch: 'main'
    })
  }
);
let frontMatter = `+++
title = "{title}"
date = "{date}"
draft = false
`;

if (tags.length) {
  frontMatter += `tags = [{tags.map(t => `"{t}"`).join(', ')}]\n`;
}

frontMatter += `+++\n\n{content}`;