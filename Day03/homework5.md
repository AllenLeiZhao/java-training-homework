## 1.how to create a thread( 4 ways, write code)
- extends Thread
```java
    Thread t = new Thread(() -> System.out.println("Thread is running"));
    t.start();
```
- implements Runnable
```java
    class MyRunnable implements Runnable {
        @Override
        public void run() {
            System.out.println("I am running.");
        }
    }
    Thread t = new Thread(new MyRunnable());
    t.start();
```
- implements Callable
```java
    class MyCallable implements Callable<Integer> {
        @Override
        public Integer call() throws Exception {
            return 200;
        }
    } 
    FutureTask<Integer> task = new FutureTask<>(new MyCallable());
    Thread t = new Thread(task);
    t.start();
```
- Thread Pool
```java
    // fixed pool:
    ExecutorService pool1 = Executors.newFixedThreadPool(4);
    // cached pool:
    ExecutorService pool2 = Executors.newCachedThreadPool();
    // single pool:
    ExecutorService pool3 = Executors.newSingleThreadExecutor();
    // custom pool:
    ExecutorService pool4 = new ThreadPoolExecutor(
        5,  // corePoolSize
        10, // maximumPoolSize
        2L,  // keepAliveTime
        TimeUnit.SECONDS, // time unit
        new ArrayBlockingQueue<>(3), // work queue
        Executors.defaultThreadFactory(), // factory
        new ThreadPoolExecutor.AbortPolicy()  // reject handler
    );
```

## 2.thread lifecycle, how does thread transfer from one state to another 
- NEW: thread is created but start() not called yet
- RUNNABLE: start() is called, thread is running or waiting for CPU
- BLOCKED: waiting to acquire a synchronized lock
- WAITING: waiting indefinitely for another thread
  - triggered by: wait(), join()
- TIMED_WAITING: waiting for a specified time
  - triggered by: sleep(n), wait(n), join(n)
- TERMINATED: run() has finished execution

State transitions:
NEW → RUNNABLE: call start()
RUNNABLE → BLOCKED: waiting for synchronized lock
RUNNABLE → WAITING: call wait() or join()
RUNNABLE → TIMED_WAITING: call sleep(n), wait(n), join(n)
BLOCKED/WAITING/TIMED_WAITING → RUNNABLE: lock acquired / notified / timeout
RUNNABLE → TERMINATED: run() completes

## 3.how does thread pool work

A thread pool pre-creates a group of threads to avoid the overhead of 
repeatedly creating and destroying threads for each task.

Execution flow:
1. New task submitted
2. Core thread available? → execute immediately
3. Core threads all busy → add to workQueue
4. workQueue full → create temporary thread (up to maximumPoolSize)
5. maximumPoolSize reached → trigger rejection handler

7 key parameters:
- corePoolSize: number of permanent threads
- maximumPoolSize: maximum number of threads (core + temporary)
- keepAliveTime: how long temporary threads wait before being destroyed
- TimeUnit: time unit for keepAliveTime
- workQueue: queue to hold waiting tasks (use ArrayBlockingQueue)
- threadFactory: responsible for creating new threads
- rejectionHandler: policy when pool is full
  - AbortPolicy: throw exception
  - DiscardPolicy: silently discard
  - DiscardOldestPolicy: discard oldest task in queue
  - CallerRunsPolicy: caller thread executes the task

Why use thread pool?
- Avoid overhead of creating/destroying threads repeatedly
- Control maximum number of concurrent threads
- Prevent OutOfMemoryError from unlimited thread creation
- Better resource management and performance

## 4.what is the potential problem for the newCachedThreadPool and newFixedThreadPool and why
## 4. What is the potential problem for newCachedThreadPool and newFixedThreadPool?

newCachedThreadPool:
- corePoolSize = 0, maximumPoolSize = Integer.MAX_VALUE
- Problem: when many tasks arrive simultaneously, it creates unlimited threads
- This can cause OutOfMemoryError (OOM) due to too many thread objects in heap

newFixedThreadPool:
- corePoolSize = maximumPoolSize = n (fixed)
- Uses LinkedBlockingQueue with no size limit (Integer.MAX_VALUE)
- Problem: when tasks arrive faster than threads can process them,
  the queue grows infinitely → OutOfMemoryError (OOM)

Both can cause OOM but for different reasons:
- newCachedThreadPool → too many threads
- newFixedThreadPool → too many tasks in queue

Solution: use custom ThreadPoolExecutor with bounded queue
    ```java
    new ThreadPoolExecutor(
        5, 10, 2L, TimeUnit.SECONDS,
        new ArrayBlockingQueue<>(100), // bounded queue!
        Executors.defaultThreadFactory(),
        new ThreadPoolExecutor.AbortPolicy()
    );
    ```
## 5.what is Future
Future represents the result of an asynchronous computation.
It is returned when you submit a Callable to a thread pool or wrap it in FutureTask.
    ```java
    Future<Integer> future = pool.submit(() -> 200);
    Integer result = future.get(); // blocks main thread until result is ready
    ```

Limitations:
- future.get() blocks the main thread
- Cannot chain multiple tasks
- Cannot combine multiple futures
- No exception handling callback

## 6.what is CompletableFuture
CompletableFuture is an enhanced Future introduced in Java 8.
It supports non-blocking task chaining and combination without blocking the main thread.

    CompletableFuture
        .supplyAsync(() -> getFromDB())
        .thenApplyAsync(data -> callAPI(data))
        .thenAccept(result -> System.out.println(result));

## 7.Future vs CompletableFuture
| | Future | CompletableFuture |
|---|---|---|
| Blocking | ✅ get() blocks | ❌ non-blocking |
| Task chaining | ❌ | ✅ thenApply, thenApplyAsync |
| Combine tasks | ❌ | ✅ allOf, anyOf |
| Exception handling | ❌ | ✅ exceptionally, handle |
| Introduced in | Java 5 | Java 8 |

Use Future: simple async task with one result
Use CompletableFuture: multiple async tasks, chaining, combining

## 8.Lock vs synchronized
synchronized:
- Built-in Java keyword
- Automatically acquires and releases lock
- Cannot be interrupted while waiting for lock
- Cannot try to acquire lock without blocking

    synchronized(this) {
        // lock acquired automatically
        // lock released automatically when block exits
    }

Lock (ReentrantLock):
- Interface from java.util.concurrent.locks
- Must manually lock() and unlock()
- More flexible: tryLock(), lockInterruptibly()
- Supports fairness: new ReentrantLock(true) → fair lock
- Supports multiple conditions
    ```java
    ReentrantLock lock = new ReentrantLock();
    lock.lock();
    try {
        // critical section
    } finally {
        lock.unlock(); // must manually unlock!
    }
    ```

| | synchronized | Lock |
|---|---|---|
| Lock/unlock | Automatic | Manual |
| Interruptible | ❌ | ✅ lockInterruptibly() |
| Try lock | ❌ | ✅ tryLock() |
| Fair lock | ❌ | ✅ new ReentrantLock(true) |
| Nested/reentrant | ✅ | ✅ |
| Recommended | Simple scenarios | Complex scenarios |

## 9. what is wait(), notify(), notifyAll(), join()
- wait(): releases the lock and suspends the current thread until 
  notify() or notifyAll() is called. Must be called inside a synchronized block.
- notify(): wakes up one randomly chosen thread that is in WAITING state.
  The awakened thread must re-acquire the lock before continuing.
- notifyAll(): wakes up all threads in WAITING state.
  All awakened threads compete for the lock, only one can proceed at a time.
- join(): makes the current thread wait until the target thread has finished execution.
  Can specify timeout: join(1000) waits at most 1 second.

## 10.commonly used CompletableFuture api and write demo code for each of them
### runAsync / supplyAsync
    ```java
    // runAsync: no return value
    CompletableFuture.runAsync(() -> System.out.println("running..."));

    // supplyAsync: has return value
    CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> 200);
    ```

### thenApply / thenApplyAsync
    ```java
    // thenApply: synchronous, runs in same thread
    CompletableFuture<String> f = CompletableFuture
        .supplyAsync(() -> 200)
        .thenApply(result -> "result is: " + result);

    // thenApplyAsync: asynchronous, runs in different thread
    CompletableFuture<String> f = CompletableFuture
        .supplyAsync(() -> 200)
        .thenApplyAsync(result -> "result is: " + result);
    ```

### handle / exceptionally
    ```java
    // exceptionally: handle exception, return default value
    CompletableFuture<Integer> f = CompletableFuture
        .supplyAsync(() -> { throw new RuntimeException("error!"); })
        .exceptionally(e -> {
            System.out.println(e.getMessage());
            return -1; // default value
        });

    // handle: handle both success and failure
    CompletableFuture<Integer> f = CompletableFuture
        .supplyAsync(() -> 200)
        .handle((result, e) -> {
            if (e != null) return -1; // error
            return result;            // success
        });
    ```

### thenCompose / thenCombine
    ```java
    // thenCompose: chain two dependent futures (output of one is input of next)
    CompletableFuture<String> f = CompletableFuture
        .supplyAsync(() -> 1)
        .thenCompose(id -> CompletableFuture.supplyAsync(() -> "user:" + id));

    // thenCombine: combine two independent futures
    CompletableFuture<Integer> f1 = CompletableFuture.supplyAsync(() -> 100);
    CompletableFuture<Integer> f2 = CompletableFuture.supplyAsync(() -> 200);
    CompletableFuture<Integer> combined = f1.thenCombine(f2, (r1, r2) -> r1 + r2);
    System.out.println(combined.get()); // 300
    ```

### allOf / anyOf
    ```java
    // allOf: wait for all futures to complete
    CompletableFuture<Void> all = CompletableFuture.allOf(f1, f2, f3);
    all.join(); // wait for all

    // anyOf: complete when any one future completes
    CompletableFuture<Object> any = CompletableFuture.anyOf(f1, f2, f3);
    System.out.println(any.get()); // result of fastest future
    ```
