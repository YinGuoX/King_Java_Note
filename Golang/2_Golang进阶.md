# Golang进阶

- 主要参考自：[Go 语言设计与实现 | Go 语言设计与实现](https://draveness.me/golang/)

## 1. 并发编程

### 1.1 上下文：Context

- 有什么用？
  
  - context.Context是Go中用来设置截止日期、同步信号、传递请求相关值的结构体
  
  - 在多个Goroutine组成的树中同步取消信号，以减少对资源的消耗和占用！
  
  - 一般不使用context.Context的传值功能，一般使用场景也只是传递请求对应用户的认证令牌、分布式追踪的请求ID

- 详细待续

### 1.2 同步原语和锁

- 什么是同步原语(Synchoronization Primitives)？
  
  - 锁是一种并发编程中的同步原语，能够保证多个Goroutine在访问同一片内存时不会出现竞争条件等问题

- Go常见的同步原语：
  
  - sync.Mutex、sync.RWMutex、sync.WaitGroup、Sync.Once、sync.Cond
  
  - errgroup、semaphore、singleflight

- Mutex：
  
  - 如何实现？什么组成？
    
    - 状态
    
    - 信号量
  
  - 有什么状态？
  
  - 正常模式和饥饿模式？为了确保互斥锁的公平性
    
    - 刚唤醒的Goroutine和新建的Goroutine的竞争问题
  
  - 如何加锁？(需要考虑不同的模式)
    
    - 抢到锁：CAS=>设置信号量+状态
    
    - 没抢到：lockSlow()方式进行自旋
      
      - 判断当前 Goroutine 能否进入自旋（这一步条件很苛刻）；
      
      - 通过自旋等待互斥锁的释放；
      
      - 计算互斥锁的最新状态；
      
      - 更新互斥锁的状态并获取锁；
    
    - 如何判断Goroutine可以进入自旋？
      
      - 互斥锁只有在普通模式才能进入自旋
      
      - [`runtime.sync_runtime_canSpin`](https://draveness.me/golang/tree/runtime.sync_runtime_canSpin) 需要返回 `true`：
        
        - 运行在多 CPU 的机器上；
        
        - 当前 Goroutine 为了获取该锁进入自旋的次数小于四次；
        
        - 当前机器上至少存在一个正在运行的处理器 P 并且处理的运行队列为空；
    
    - 自旋进行什么操作？
      
      - 进入自旋就会调用[`runtime.sync_runtime_doSpin`](https://draveness.me/golang/tree/runtime.sync_runtime_doSpin) 和[`runtime.procyield`](https://draveness.me/golang/tree/runtime.procyield) 并执行 30 次的 `PAUSE` 指令
      
      - ...
      
      - 在正常模式下，[`sync.Mutex.Lock`](https://draveness.me/golang/tree/sync.Mutex.Lock)代码会设置唤醒和饥饿标记、重置迭代次数并重新执行获取锁的循环；
      
      - 在饥饿模式下，当前 Goroutine 会获得互斥锁，如果等待队列中只存在当前 Goroutine，互斥锁还会从饥饿模式中退出；
  
  - 如何解锁？(需要考虑不同的模式)
    
    - 使用 [`sync/atomic.AddInt32`](https://draveness.me/golang/tree/sync/atomic.AddInt32) 函数快速解锁，这时会发生下面的两种情况：
      
      - 如果该函数返回的新状态等于 0，当前 Goroutine 就成功解锁了互斥锁；
      - 如果该函数返回的新状态不等于 0，这段代码会调用 [`sync.Mutex.unlockSlow`](https://draveness.me/golang/tree/sync.Mutex.unlockSlow) 开始慢速解锁：
        - 在正常情况下会根据当前互斥锁的状态，分别处理正常模式和饥饿模式下的互斥锁：
        - ...

- RWMutex：
  
  - 有什么用？读写分离？
  
  - 读写锁的具体实现？结构体？方法？

- WaitGroup：
  
  - 有什么用？
    
    - 可以等待一组 Goroutine 的返回，一个比较常见的使用场景是批量发出 RPC 或者 HTTP 请求
    
    - 将原本顺序执行的代码在多个 Goroutine 中并发执行，加快程序处理的速度。
  
  - 具体实现？结构体？方法？

- Once：
  
  - 有什么用？
    
    - 可以保证在 Go 程序运行期间的某段代码只会执行一次。
  
  - 具体实现？结构体？方法？

- Cond：
  
  - 有什么用？
    
    - 可以让一组的 Goroutine 都在满足特定条件时被唤醒。
  
  - 具体实现？结构体？方法？

- 其他原语...
