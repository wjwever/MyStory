# [git如何撤销](https://github.com/wjwever/gitblog/issues/23)

* 情况一：文件被修改了，但未执行git add操作（working tree内撤销）
git checkout fileName
git checkout .

* 文件执行了git add操作，但想撤销对其的修改（index内回滚）
#取消暂存
git reset HEAD fileName
#撤销修改
git checkout fileName


* git add后的代码，想撤销
git reset HEAD .
git reset HEAD a.txt

* 已在本地进行了多次git commit操作，现在想撤销到其中某次Commit
git reset [--hard|soft|mixed|merge|keep] [commit|HEAD]








https://blog.csdn.net/ligang2585116/article/details/71094887