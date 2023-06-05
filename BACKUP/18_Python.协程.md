# [Python 协程](https://github.com/wjwever/gitblog/issues/18)

## 上代码：
```python
!/usr/bin/env python3
# async.py

import asyncio

async def count():
    print("One")
    await asyncio.sleep(1)
    print("Two")

async def main():
    await asyncio.gather(count(), count(), count())

asyncio.run(main())
```
## 执行结果
```python
$ python3 async.py
One
One
One
Two
Two
Two
```
## 知识要点
* aync 关键字定义一个异步执行函数，这个函数在执行中间可以挂起，交给其他的协程执行
* await 表示进入一个耗时操作，此时可以把CPU让给其他协程
* 在 async 函数main的里面，asyncio.gather() 方法将多个异步任务（三个 count()）包装成一个新的异步任务，必须等到内部的多个异步任务都执行结束，这个新的异步任务才会结束

***
参考  
http://www.ruanyifeng.com/blog/2019/11/python-asyncio.html
https://cloud.tencent.com/developer/inventory/5723/article/1639528
https://zhuanlan.zhihu.com/p/137057192