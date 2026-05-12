---
title: Hexo升级记录
date: 2026-05-12 22:00:00
tags: [Hexo]
categories: Hexo
description: 记录Hexo从3.4.1升级到8.1.2、NexT主题从5.1.3升级到8.27.0的完整过程
---

## 背景

当前 Node.js 版本为 v24.15.0，而 Hexo 仍停留在 3.4.1 版本，NexT 主题还是 iissnan 版的 5.1.3，已经无法兼容。

## 升级步骤

### 1. Hexo 框架升级

直接更新 `package.json` 中的 hexo 及相关依赖到最新版本：

- hexo: 3.4.1 → 8.1.2
- hexo-renderer-marked, hexo-renderer-ejs, hexo-renderer-stylus 等渲染器全部更新

### 2. NexT 主题升级

旧版 NexT 5.1.3 (iissnan) 使用的是 `.swig` 模板，Hexo 8.x 已移除 swig 渲染支持，且旧版主题已不再维护。

将主题从 `themes/next/` 目录管理改为 npm 包管理：

```bash
npm install hexo-theme-next@latest
```

新版 NexT 8.x 使用 `.njk` (Nunjucks) 模板，配置方式也发生了变化——不再修改主题目录下的 `_config.yml`，而是在站点根目录创建 `_config.next.yml` 进行覆写。

### 3. 配置迁移

关键配置变化：

- **Scheme**: Mist（保持不变）
- **菜单**: 菜单项改为带图标的格式（如 `home: / || fa fa-home`）
- **社交链接**: GitHub、邮箱配置
- **头像**: `/images/avatar.gif`
- **评论**: 接入 Livere（来必力），`livere_uid` 沿用旧配置
- **本地搜索**: 启用，配合 `hexo-generator-searchdb`
- **Fancybox**: 启用图片放大效果
- **Pace**: 加载进度条
- **SWIG 模板 → Nunjucks**: 由于旧主题的 `custom.swig` 不再适用，通过 NexT 8.x 的 `custom_file_path` 功能，在 `source/_data/body-end.njk` 注入自定义脚本

### 4. 自定义效果恢复

升级后通过 `source/_data/body-end.njk` 恢复了以下自定义效果：

- **标题切换动画**: 离开页面显示「啊咧?真由氏的怀表停了呢~」，回来显示「这一切都是命运石之门的选择！」
- **看板娘**: 调用 live2d 看板娘
- **点击小红心**: 页面任意位置点击出现爱心飘散效果

### 5. 遇到的问题

| 问题 | 原因 | 解决 |
|------|------|------|
| `url 不是有效 URL` | `_config.yml` 中 url 缺少 `https://` 前缀 | 补上 `https://` |
| `Spawn failed` 部署失败 | git 用户配置缺失 | 配置本地 git user/email |
| 页面显示 swig 原始代码 | Hexo 8.x 不再支持 swig 模板 | 升级 NexT 到 8.x |
| 无法找到 `_layout.swig` | 旧模板与新渲染器不兼容 | 同上，升级 NexT |

### 6. 构建与部署

升级后构建正常：

```
173 files generated in 1.6s
```

已通过 `hexo deploy` 部署到 GitHub Pages。
