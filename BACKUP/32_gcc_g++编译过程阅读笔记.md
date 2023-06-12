# [gcc_g++编译过程阅读笔记](https://github.com/wjwever/gitblog/issues/32)

# gcc/g++编译过程阅读笔记

1. 预处理，生成.i的文件

   ```c++
   g++ -E main.cpp > main.i 
   ```

   预处理后不生成文件，因此需要重定向到一个文件中

2. 将预处理后的文件转换成汇编语言，生成.s文件

   ```c++
   g++ -S main.cpp
   ```

3. 汇编变为目标代码(机器代码)生成.o的文件

   ```c++
   g++ -c main.cpp 
   ```

4. 连接目标代码,生成可执行程序

   ```c++
   g++ main.o -o main //生成的可执行程序名为main ，如果执行命令 　g++ main.o  这样默认生成a.out，也就是main与a.out是一个只是名字不同而已
   ```

那么，是在哪里实现”cout”函数的呢？系统把这些函数实现都被做到名为stdc++的库文件中去了，在没有特别指定时，g++会到系统默认的搜索路径**”/usr/lib”**下进行查找，也就是链接到stdc++库函数中去，这样就能实现函数”cout”了，而这也就是链接的作用。

# gcc/g++之动态静态编译库函数

将这几个文件编译成动态库libdynamic.so。编译命令如下：

``` 
g++ dynamic_a.cpp dynamic_b.cpp dynamic_c.cpp -fPIC -shared -o libdynamic.so
```

## 参数说明

-shared：该选项指定生成动态连接库

-fPIC：表示编译为位置独立的代码，不用此选项的话编译后的代码是位置相关的所以动态载入时是通过代码拷贝的方式来满足不同进程的需要，而不能达到真正代码段共享的目的。

### 将main.cpp与libdynamic.so链接成一个可执行文件main。命令如下：

```
g++ main.cpp -L. -ldynamic -o main
```

-L.：表示要连接的库在当前目录中

-ldynamic：编译器查找动态连接库时有隐含的命名规则，即在给出的名字前面加上lib，后面加上.so来确定库的名称

测试可执行程序main是否已经链接的动态库libdynamic.so，如果列出了libdynamic.so，那么就说明正常链接了。可以执行以下命令：

```
ldd main
```

