# [Linux限速解压](https://github.com/wjwever/gitblog/issues/25)

需要安装pv工具
cat test.sql.tar | pv -e -t -b -L 10M |tar -xf -
https://linux.cn/article-6734-1.html
https://loveyu.org/5693.html