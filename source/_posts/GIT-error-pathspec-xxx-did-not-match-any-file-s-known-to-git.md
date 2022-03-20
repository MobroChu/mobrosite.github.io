---
title: 'GIT: error: pathspec ''xxx did not match any file(s) known to git'
date: 2018-06-05 10:00:56
tags: git
description: 切换分支的时候，报了标题这么个错误，现记录一下解决方法。
---

切换分支的时候，报了标题这么个错误，error: pathspec ''xxx did not match any file(s) known to git.
![](/images/201806/WX20180605-100532.png)
看见不能切换分支，我首先 `git status` 查看了一下当前状态，如下图
![](/images/201806/WX20180605-100350.png)  
然后，就会发现，其实我的这个错误非常明显，就是在我的 `beat` 分支下有文件修改，所以切换不了。ok，解决方法：
1. 如果修改的这些文件没什么用，完全可以删除。（我这儿的这些文件就是 mac 自动生成的，完全就可以使用这种方法）
``` bash
rm -rf img/newIndex/.DS_Store img/newIndex/mobile/.DS_Store .DS_Store img/.DS_Store  
```
2. 根目录下添加 `.gitignore` 文件，将 `.DS_Store` 文件忽略即可

我这儿问题比较简单。接下来，记录一下此类问题的其他解决方案。参考了[文章](https://stackoverflow.com/questions/33628862/git-error-pathspec-xxx-did-not-match-any-files-known-to-git)
``` bash
## 清除缓存
git rm -r --cached .  
## git add
git add .
## git commit
git commit -m "hopefully fixed pathspec error"
```

