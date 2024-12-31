---
title: 并发问题
date: 2024-12-12 10:48:34
tags:
  - 并发
  - 操作系统
  - 考研
  - "408"
---
# 线程的三种状态
## 运行（Running）状态：

定义：线程正在CPU上执行其指令。

特点：处于此状态的线程正在占用处理器资源，执行任务。
## 就绪（Ready）状态：

定义：线程已经准备好运行，等待操作系统的调度分配CPU资源。

特点：线程已具备执行条件，但由于CPU被其他线程占用，暂时未获得执行权。
## 阻塞（Blocked）状态：

定义：线程因等待某些条件（如I/O操作完成、锁的释放等）而无法继续执行，暂时无法进入就绪状态。

特点：线程在此状态下不会被调度执行，直到等待的条件满足。
# 基础问题
```text
def producer():
    accquire_lock(mutex_lock);
    cv.wait(()=>condition)//不满足则被设置为阻塞态，直到notify以后，再次检查条件变量
    produce()
    cv.notify_one(consumer);
    release(mutex_lock);
```
```text
def consumer():
    accquire_lock(mutex_lock);
    cv.wait(()=>condition)//不满足则被设置为阻塞态
    produce()
    cv.notify_one(producer);
    release(mutex_lock);
```
# Semaphore（信号量）
直接上代码
```c++
#include "Semaphore.h"

Semaphore::Semaphore(int count) : count(count) {}//Semaphore有一个初始信号量

void Semaphore::P() {
    std::unique_lock<std::mutex> lock(mtx);
    cv.wait(lock, [this] { return count > 0; });
    --count;
}

void Semaphore::V() {
    std::unique_lock<std::mutex> lock(mtx);
    ++count;
    cv.notify_one();
}
```

