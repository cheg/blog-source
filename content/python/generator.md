---
title: "Generator"
date: 2018-01-05T21:00:45+08:00
draft: true
---

<!--more-->
## 1. 生成器和协程介绍

* 生成器是一个函数，它产生一个结果序列而不是一个值
* 使用`yield`语句，它生成一个值序列代替返回一个值
* 调用一个生成器函数创建一个生成器对象，然而它并不开始运行这个函数
* 这个函数仅在next()时运行
* `yield`产生一个值，但函数进入suspend状态
* 再执行next()时它又恢复
* 当生成器全部返回，迭代结束

```python
def countdown(n):
    print "Counting down from", n
    while n > 0:
        yield n n -= 1
>>> x = countdown(10)
>>> x
<generator object at 0x58490>   # 函数并未开始运行
>>> x.next()
Counting down from 10           # 函数开始运行
10                              # 函数进入suspend状态
>>> x.next()
9
...                             # 执行多次next()
>>> x.next()
1
>>> x.next()                    # 迭代结束
Traceback (most recent call last):
    File "<stdin>", line 1, in ?
    StopIteration
>>>
```

### 生成器作为管道(pipe)

* 生成器最强大的应用之一就是设置成管道, 类似于Unix中的`shell pipe`

```python
# A Python version of Unix 'tail -f'
import time
def fellow(thefile):
    thefile.seek(0, 2)          # Go to the end of the file
    while True:
        line = thefile.readline()
        if not line:
            time.sleep(0.1)     # Sleep briefly
            continue
        yield line

# Print all server log entries containing 'python'
def grep(pattern, lines):
    for line in lines:
        if pattern in line:
            yield line

# Set up a processing pipe : tail -f | grep python
logfile = open(access-log)
loglines = fellow(logfile)
pylines = grep('python', loglines)

# Pull results out of the processing pipeline
for line in pylines:
    print(line)
```

### Yield作为表达式

```python
def grep(pattern):
    print "Looking for %s" % pattern
    while True:
        line = (yield)      # yield 在表达式右边
        if pattern in line:
            print(line)
```
### Coroutines(协程)

* 生成器函数可以接受发送给他的值,并进行处理

```python
>>> g = grep("python")
>>> g.next()                # 启动生成器 
Looking for python
>>> g.send("Yeah, but no, but yeah, but no")    # 向生成生成器发送值
>>> g.send("A series of tubes")
>>> g.send("python generators rock!")
python generators rock!
>>> g.close()
```
1. 所有coroutines(协程)必须通过调用next()或send(None)进行启动
2. next() 或 send(None) 将程序向前执行到 yield 表达式处（上例中是line = (yield)),
3. 此时程序等待接收一个值

* 使用装饰器(Decorator)

调用.next()很容易忘记,可以用装饰器封装进程解决这个问题

```python
def coroutine(func):
    def start(*args, **kwargs):
        cr = func(*args, **kwargs)
        cr.next()
        return cr
    return start

@couroutine
def grep(pattern):
    ...
```

* 使用.close() 关闭协程

协程可能无限期运行，所以需要用.close() 关闭，可以通过捕捉GeneratorExit 调用close()

```python
@coroutine
def grep(pattern):
    print "Looking for %s" % pattern
    try:
        while True:
            line = (yield)
            if pattern in line:
                print(line)
    except GeneratorExit:           # GeneratorExit自动调用close()关闭协程
        print "Going away. Goodbye"

```

* 生成器产生值,生成器产生数据是为了迭代
* 协程消费值,协程是数据的消费者。协程与迭代没有任何关系。

## 2. 协程、管道和数据流

### 管道

* 协程可以用于设置管道
* 只要将协程串联在一起并用send()推送数据穿过管道
* 管道需要一个初始化源(source)，源驱动整个管道, 源不是一个协程

```python
def source(target):
    while not done:
        item = produce_an_item()
        ...
        target.send(item)
        ...
    target.close()
```
* 管道必须有一个终点(sink)，sink收集所有发送给它的数据并处理这些数据

```python
@coroutine
def sink():
    try:
        while True:
            item = (yield)      # 接收数据
            ...                 # 处理数据
    except GeneratorExit:       # 处理.close()
        # Done
        ...
```

管道示例：

```python
# 一个source，模拟Unix 'tail -f'
import time
def follow(thefile, target):
    thefile.seek(0, 2)
    while True:
        line = thefile.readline()
        if not line:
            time.sleep(0.1)
            continue
        target.send(line)

# 一个sink, 打印行内容
@coroutine
def printer():
    while True:
        line = (yield)
        print line

# 连接在一起
f = open('access-log')
follow(f, printer())

```
### 管道过滤器

* 管道中间部分即接收又发送，通常执行各种数据类型的转换、过滤、路由(分发)

```python
@coroutine
def filter(target):
    while True:
        item = (yiedl)
        ...                 # Transform/filter item
        target.send(item)   # 发送到下一阶
```

过滤器示例：

```python
@coroutine
def grep(pattern, target):
    while True:
        line = (yield)          # 接收一行
        if pattern in line:
            target.send(line)   # 发送到下一阶

# 连接起来
f = open('access-log')
follow(f, 
        grep('python', 
        printer()))
```

* 作为分发器

你使用协程可以将数据发送到多个目标，比如广播：

```python
@coroutine
def broadcast(targets):
    while True:
        item = (yield)
        for target in targets:
            target.send(item)

# 
f = open('access-log')
p = printer()
follow(f, 
        broadcast([grep('python', p),
                    grep('ply', p),
                    grep('swig', p)]))
```

* 协和提供比迭代器更强大的数据路由可能性
* 如果你创建了一个简单数据处理组件的集合，你可以将它们粘在一起成为复杂的管道、分支、合并的分配程序



