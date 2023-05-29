# [Ctags使用方法](https://github.com/wjwever/gitblog/issues/13)

- 在代码目录下生成ctags：
```bash
ctags -R   --languages=c++ --langmap=c++:+.inl -h +.inl --c++-kinds=+px --fields=+aiKSz --extra=+q   --exclude=platform_mgr/* --exclude=android-ndk-r11c/* --exclude=output/* -f ./tags
```

- vimrc指定tag位置
```bash
set tags=tags;/
```

- vimrc注释autochdir
```
" set autochdir                   " 自动切换工作目录为当前文件所在的目录
```

> 参考：https://blog.csdn.net/sinat_30603081/article/details/111192247