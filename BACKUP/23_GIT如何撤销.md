# [GIT如何撤销](https://github.com/wjwever/gitblog/issues/23)


* git 再工作区的代码，尚未git add
git checkout -- a.txt # 丢弃某个文件，或者
git checkout -- . # 丢弃全部

* git add后的代码，想撤销
git reset HEAD .
git reset HEAD a.txt

* git commit后的代码想撤销
    







https://www.jianshu.com/p/c55958563f5a