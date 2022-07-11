# `AQS`

​		全称是`AbstractQueuedSynchronized`，是阻塞式锁和相关同步器工具的框架。

**特点：**

- 用`state`属性来表示资源的状态（分为独占模式和共享模式），子类需要定义如何维护这个状态，控制获取锁和释放锁
  - `getState`：获取state状态
  - `setState`：设置state状态
  - `compareAndSetState`：cas机制设置`state`状态
  - 独占模式是只有一个线程可以访问资源，共享模式允许多个线程访问资源
- 提供了一个基于FIFO的等待队列，类似于`Monitor`的`WaitSet`
- 条件变量来实现等待，唤醒机制，支持多个条件变量，类似`Monitor`的`WaitSet`

```java
//如果获取锁失败
if(!tryAcquire(arg)){
    //入队，可以选择阻塞当前线程
}
```

```java
//如果释放锁成功
if(tryRelease(arg)){
    //让阻塞线程恢复运行
}
```

