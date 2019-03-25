---
title: hexo换机前后的需要做的
date: 2019-03-24 15:24:27
tags:
---

## 换机之前的步骤

1. 克隆项目到本地
```
 git clone 项目
```

2. 进入项目文件夹，删掉除.git外的所有。

3. 把原来hexo内所有内容复制过来。

4. 创建分支
```
git checkout -b hexo
```

5. 添加add、commit、推送，操作类似普通项目
```
git add --all
git commit -m ""
git push --set-upstream origin hexo
```

6. 发布博客和更新
```
hexo new post "新文章"
hexo d -g
```

7. git三部曲
```
git add . 
git commit -m "注释" 
git push origin hexo
```

## 换机之后的步骤

1. clone到本地
```
git clone -b hexo 项目
```

2. 发布博客和更新
```
hexo new post "新文章"
hexo d -g
```

3. git三部曲
```
git add . 
git commit -m "注释" 
git push origin hexo
```

---

参考文献： https://blog.csdn.net/heimu24/article/details/81210640
