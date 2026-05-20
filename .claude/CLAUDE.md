# 博客配置

## 基本信息
- 博客标题：石头门的选择
- 作者：Keinz
- 语言：zh-CN
- 主题：NexT（`theme: next`）
- Hexo 版本：8.1.2

## 项目路径
`D:/work/MyCode/cherrykeinz.github.io/`

## 分支策略
- **hexo 分支**：博客源码（Markdown 源文件、主题配置等）
- **master 分支**：GitHub Pages 静态页面（由 `hexo deploy` 自动部署）
- 日常开发在 hexo 分支，git push 时确认目标分支

## Git 仓库
- 源码仓库（origin）：`git@github.com:LostCherry/lostcherry.github.io.git`（hexo 分支）
- 部署仓库：`git@github.com:LostEvangel/lostevangel.github.io.git`（master 分支，通过 hexo-deployer-git）

## 部署流程
1. `npx hexo clean`
2. `npx hexo generate`
3. `git add . && git commit -m "..." && git push origin hexo`（推送源码）
4. `npx hexo deploy`（部署静态页到 GitHub Pages）

## 文章目录
`source/_posts/`

## 常用分类
Java、数据库、算法、Linux、网络、操作系统、设计模式、Hexo

## 常用标签
Java、JVM、Spring、数据库、MySQL、排序算法、设计模式、nginx、Hexo

## 日期格式
`YYYY-MM-DD HH:mm:ss`
