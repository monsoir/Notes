# Python 多线程

使用 threading 模块

## 创建一个线程

1. 引入 threading 模块
2. 定义一个函数，放在新线程中执行的函数
3. 创建线程
4. 开启线程的运行

```py
import threading # 1

# 2
def functionWillExecuteInNewThread():
    # ...
    
threadObj = threading.Thread(target=functionWillExecuteInNewThread) # 3
threadObj.start() # 4
```

## 向线程的目标函数传递参数

- target 传入目标函数名
- args 传入常规参数，常规参数作为一个 list
- kwargs 传入关键字参数，关键字参数作为一个 dict

```py
import threading

threadObj = threading.Thread(target=print, args=['Cats', 'Dogs', 'Frogs'], kwargs={'sep': ' &'})
threadObj.start()
```

上述的调用实际上等同于

```py
print('Cats', 'Dogs', 'Frogs', sep = ' &')
```

> 使用多线程的时候，需要注意并发问题，即同时读写一个变量

## 等待线程的结束

在主线程 main-thread 中，我们新开了一个线程 sub-thread 并开始了运行，但是希望在 sub-thread 运行结束后，main-thread 再继续运行下去，此时，我们使用 `join()` 方法

```py
import threading

# 现在所有的工作都是在主线程中运作

def functionRunsInSubThread():
    # do something...
    
subThread = threading.Thread(name='thread name', target=functionRunsInSubThread)
subThread()

# 注意，上面创建线程的代码，都是在主线程中执行的
# ...

# 主线程等待 subThread 执行完
subThread.join()

# 主线程继续干其他事情
# ...
```

在上面的例子中，main-thread 调用 subThread 的 join() 方法后，将会等待 subThread 执行完后才会继续向下执行

join() 方法阻塞的是调用时的线程，在上面的例子中，是在 main-thread 中调用 subThread.join(), 因此阻塞的是 main-thread

> 这种东西，可以使用在多线程下载之类的情况中

