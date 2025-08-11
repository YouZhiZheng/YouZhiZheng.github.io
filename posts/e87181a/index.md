# Velox_线程池模块


## 概述

在现代开发中，多线程可以提高程序的运行效率和响应速度，它已经成为提高应用程序性能、处理并发任务的重要手段。使用多线程需要注意线程同步、资源消耗等问题，当使用的线程数多了时，手动进行管理是十分困难的。为了解决这些问题，**线程池**作为一种有效的线程管理机制应运而生。

**线程池**会预先创建一定数量的工作线程，我们只需将待执行任务提交到线程池，线程池会负责任务的分配与执行，从而简化线程管理、减少系统频繁创建与销毁线程的开销、提高资源利用率。其核心思想是避免频繁的创建和销毁线程，减少系统开销。

## 主要特性

- **完整的生命周期管理**：线程池具有明确的运行状态，构成一个状态机，确保在任何时刻都处于可预期的状态。
- **支持暂停和恢复**：在暂停状态下，线程池会继续接收新任务，但所有工作线程将暂停执行，直到线程池被恢复。
- **支持优雅关闭**：析构函数会自动调用 `shutdown`函数，安全的销毁线程池，符合 RAII (Resource Acquisition Is Initialization) 原则。
- **支持调整工作线程数量**：可以在运行时通过 `increaseThreadCount` 和 `decreaseThreadCount` 方法动态地增加或减少工作线程的数量，以适应不同的负载需求。
- **支持自动调整工作线程数量**：引入核心线程数和最大线程数两个概念，使得线程池能在工作负载变化时自动调整线程数量。核心线程始终保留在池中，而最大线程数则限定线程池可动态扩展的上限。
- **支持配置文件**：可以通过`threadpool.yml`配置文件修改线程池的相关参数（如核心线程数、最大线程数等），无需重复编译。

## 基础知识

在正式介绍线程池模块的相关代码前，我们需要先了解一些必要的现代C&#43;&#43;编程基础知识。

### `std::atomic`

`std::atomic` 是 C&#43;&#43;11 引入的模板类，用于实现**多线程环境下的原子操作**，从而避免数据竞争。原子操作是指一个不可被中断的操作，要么完全执行，要么完全不执行，在执行过程中不会被其他线程的调度打断。[std::atomic简介](https://zhuanlan.zhihu.com/p/649663363)

#### 作用

- **替代互斥锁**：对于简单的计数器、标志位等共享状态，使用 `std::atomic` 比使用互斥锁（`std::mutex`）的开销更小，性能更高。锁通常会引起线程阻塞和上下文切换，而 **「原子操作通常由特殊的 CPU 指令实现，属于无锁（Lock-Free）编程的范畴」**。

#### 用法

```cpp
#include &lt;atomic&gt;
#include &lt;iostream&gt;
#include &lt;thread&gt;
#include &lt;vector&gt;

// 使用 std::atomic&lt;int&gt; 作为线程安全的计数器
std::atomic&lt;int&gt; counter(0);

void increment() 
{
    for (int i = 0; i &lt; 10000; &#43;&#43;i) 
    {
        // 原子地增加计数器，等价于 counter = counter &#43; 1
        // 这个操作是线程安全的
        counter.fetch_add(1, std::memory_order_relaxed);
    }
}

int main() 
{
    std::vector&lt;std::thread&gt; threads;
    for (int i = 0; i &lt; 10; &#43;&#43;i) 
    {
        threads.emplace_back(increment);
    }

    for (auto&amp; t : threads) 
    {
        t.join();
    }

    // counter.load() 原子地读取当前值
    std::cout &lt;&lt; &#34;Final counter value: &#34; &lt;&lt; counter.load() &lt;&lt; std::endl; // 输出 100000
    return 0;
}
```

#### 常用操作

- `load()`: 原子地读取值。
- `store(value)`: 原子地写入值。
- `exchange(value)`: 原子地替换为新值，并返回旧值。
- `fetch_add(arg)` / `fetch_sub(arg)`: 原子地加上/减去一个值，并返回旧值。（`&#43;=` 和 `-=` 的重载是等效的，但返回新值）。
- `compare_exchange_weak(expected, desired)` / `compare_exchange_strong(expected, desired)`: 比较并交换（CAS）操作。这是原子操作的核心，它将当前值与 `expected` 比较，如果相等，则替换为 `desired` 并返回 `true`；否则，将 `expected` 更新为当前值并返回 `false`

#### 注意事项

- **内存序（Memory Order）**：`std::atomic` 的操作可以接受一个 `std::memory_order` 参数，用来控制当前操作如何与其他线程的内存操作同步。这是 `std::atomic` 中最复杂也最关键的部分，其控制着原子操作在多线程之间的可见性和执行顺序。
  - `memory_order_relaxed`: 最宽松的顺序，只保证操作本身的原子性，不提供任何跨线程的顺序保证。
  - `memory_order_acquire`: 获取语义。在本线程中，所有后续的读写操作都不能重排到此操作之前。
  - `memory_order_release`: 释放语义。在本线程中，所有之前的读写操作都不能重排到此操作之后。
  - `memory_order_acq_rel`: 同时具备获取和释放语义。
  - `memory_order_seq_cst`: 顺序一致性，最强的内存序，保证所有线程看到的所有原子操作都有一个全局一致的顺序。这是所有原子操作的**默认值**。
- **不是万能的**：`std::atomic` 适用于单个变量的原子操作。如果需要保护多个变量，或者需要进行一系列复杂的操作，那么还是应该使用 `std::mutex`。
- **可平凡复制**：`std::atomic&lt;T&gt;` 要求类型 `T` 是可平凡复制的（Trivially Copyable），这意味着它可以用 `memcpy` 在内存中安全地复制。

---
看了上面的注意事项，你可能还在想 &#34;内存序&#34; 是什么鬼？有啥用？那我们就通过下面内容来了解一下：
**1. 为什么需要内存序？**

- **编译器和 CPU 的优化**
  现代编译器和 CPU 为了提高性能，会对代码执行顺序进行调整（重排序），只要不改变单线程程序的语义。例如：

  ```cpp
    x = 1;    // 语句 A
    y = 2;    // 语句 B
  ```

  在单线程中，编译器 / CPU 可能会交换语句 A 和 B 的执行顺序，因为它们之间没有**依赖关系**。但在多线程环境中，这种重排序可能会导致其他线程看到不一致的内存状态。

- **多处理器系统的缓存一致性问题**
  在多核系统中，每个 CPU 核心有自己的缓存。当一个线程修改变量时，这个修改可能先在缓存中生效，而其他核心的缓存尚未更新。如果没有适当的同步机制，其他线程可能读取到旧值。
  
- **性能与正确性的权衡**
  如果所有操作都强制按顺序执行并保证全局可见性，虽然简单但会导致性能下降。内存序允许开发者根据实际需求放宽约束，提高性能。
  
**2. 内存序如何解决上述问题？**
C&#43;&#43; 提供了 6 种内存序（实际常用 4 种），通过控制 **内存可见性** 和 **指令重排序** 来平衡性能和正确性：
**（1）** `std::memory_order_relaxed`
该内存序只保证操作本身的原子性，不提供任何跨线程的顺序保证，例如：

```cpp
std::atomic&lt;int&gt; counter(0);

// 线程 1
counter.fetch_add(1, std::memory_order_relaxed);  // 仅保证原子性

// 线程 2
int value = counter.load(std::memory_order_relaxed);  // 可能会读到 0, 也可能会读到1
```

因为`relaxed`不提供任何跨线程的顺序保证，所以线程2的读取到`counter`值可能为0（在加操作前读取），也可能为1（在加操作后读取）。**该内存序只适用于计数器自增等无需同步的操作**。

**（2）**`std::memory_order_release` 和 `std::memory_order_acquire`
在同一线程中`release`强制要求此操作之前的读写操作都**不能重排到此操作后**；在同一线程中`acquire`强制要求此操作之后的读写操作都**不能重排到此操作之前**。两者配合使用可以实现**同步原语**，是一种轻量级的同步机制。直接看示例：

```cpp
std::atomic&lt;bool&gt; ready{false};
int data = 0;

// 线程1（生产者）
data = 42; // 非原子操作
ready.store(true, std::memory_order_release); // 发布数据

// 线程2（消费者）
while (!ready.load(std::memory_order_acquire)); // 等待数据
assert(data == 42); // 必定成功
```

- `memory_order_release` 保证 `data=42` 不会重排到 `ready=true` 之后
- `memory_order_acquire` 保证 `assert` 不会重排到 `while` 之前

**（3）**`std::memory_order_seq_cst`（默认）
这是原子操作中最安全的内存序，代价为性能较低，它保证了：所有线程看到的所有**原子操作**，具有**全局统一**的执行顺序（因为会更新全部核的缓存），且**同一线程内的执行顺序与代码中写的顺序一致**。直接看示例：

```cpp
std::atomic&lt;int&gt; x{0};
std::atomic&lt;int&gt; y{0};
int r1, r2;

// 线程1
void thread1() 
{
    x.store(1, std::memory_order_seq_cst); // A
    r1 = y.load(std::memory_order_seq_cst); // B
}

//线程2
void thread2() 
{
    y.store(1, std::memory_order_seq_cst); // C
    r2 = x.load(std::memory_order_seq_cst); // D
}

int main()
{
    ......

    std::cout &lt;&lt; &#34;r1: &#34; &lt;&lt; r1 &lt;&lt; &#34;, r2: &#34; &lt;&lt; r2 &lt;&lt; std::endl;
    return 0;
}

```

在上面的代码中，合法的执行顺序只可能为下面的6种：

```bash
A B C D
A C B D
A C D B
C D A B
C A D B
C A B D
```

`seq_cst`规定了A必须在B前面，C必须在D前面（执行顺序与代码中写的顺序一致），且当某个原子操作执行完成后，其他线程也能**马上**看到该操作。所以，不存在输出`r1: 0, r2: 0` 的情况，因为 `r1` 为 0 说明A已经执行（线程2能马上看到A操作的结果，即`x`的值被修改为了1），`r2`不可能为0；同理 `r2` 为 0 说明C已经执行，`r1`不可能为0。

==注意：==内存序十分重要和复杂，刚开始使用时推荐拷打AI老师（建议同时拷打3个），逐步积累使用经验。

### `std::async`

`std::async` 是一个 C&#43;&#43;11 引入的函数模板，它以一种简单直接的方式异步地运行一个可调用对象（如函数、lambda 表达式），并返回一个 `std::future`，该 `std::future` 将在未来的某个时刻持有该异步任务的计算结果。

#### 作用

- **简化异步编程**：`std::async` 是启动异步任务最简单的方法之一。它封装了线程的创建和管理，让开发者可以像调用普通函数一样启动一个并发任务，而无需手动创建 `std::thread` 对象。
- **获取返回值和异常**：它自动将任务的返回值或抛出的异常捕获并存入返回的 `std::future` 对象中，使得在主线程中获取结果和处理异常变得非常方便。
- **抽象化线程管理**：它允许运行时库（Runtime Library）根据系统负载和可用资源来决定任务的执行方式，例如是在新线程中运行还是延迟执行，从而可能实现更好的性能和资源利用。

#### 用法

`std::async` 的基本用法是传入一个可调用对象和其所需的参数。

```cpp
#include &lt;iostream&gt;
#include &lt;future&gt;
#include &lt;string&gt;
#include &lt;chrono&gt;
#include &lt;thread&gt;

// 一个耗时的任务，接收一个字符串参数并返回其长度
size_t string_length_task(const std::string&amp; s) 
{
    std::cout &lt;&lt; &#34;Task running in thread: &#34; &lt;&lt; std::this_thread::get_id() &lt;&lt; std::endl;
    std::this_thread::sleep_for(std::chrono::seconds(2));
    return s.length();
}

int main() 
{
    std::string my_string = &#34;Hello, C&#43;&#43; Concurrency!&#34;;
    std::cout &lt;&lt; &#34;Main thread: &#34; &lt;&lt; std::this_thread::get_id() &lt;&lt; std::endl;

    // 启动异步任务。my_string 被复制或移动到任务中。
    // 使用 std::launch::async 策略确保任务在新线程中执行。
    std::future&lt;size_t&gt; fut = std::async(std::launch::async, string_length_task, my_string);

    std::cout &lt;&lt; &#34;Task launched. Main thread continues working...&#34; &lt;&lt; std::endl;

    // ... 主线程可以执行其他工作 ...

    std::cout &lt;&lt; &#34;Waiting for result from async task...&#34; &lt;&lt; std::endl;
    // 调用 fut.get() 会阻塞，直到任务完成并返回结果
    size_t length = fut.get();

    std::cout &lt;&lt; &#34;The length of the string is: &#34; &lt;&lt; length &lt;&lt; std::endl;

    return 0;
}
```

#### 常用操作

`std::async` 本身是一个函数，其操作就是调用它。关键在于如何配置它的行为，这通过启动策略（launch policy）参数来控制：

- **`std::async(std::launch::async, f, args...)`**:
  - **行为**: 保证可调用对象 `f` 在一个新的线程上异步执行，类似于创建了一个 `std::thread`。
  - **效果**: 任务会立即开始（或尽快由操作系统调度），与调用者并发执行。

- **`std::async(std::launch::deferred, f, args...)`**:
  - **行为**: 任务被延迟执行。它不会立即启动，而是在返回的 `std::future` 上首次调用 `get()` 或 `wait()` 时，才会在**调用 `get()` 或 `wait()` 的那个线程**上同步执行。
  - **效果**: 没有并发，只是将函数的执行推迟了。
- **`std::async(f, args...)`** (不指定策略，使用默认值):
  - **行为**: 这是默认策略，等价于 `std::launch::async | std::launch::deferred`。实现可以自由选择是在新线程中立即执行，还是延迟执行。
  - **效果**: 行为不确定，取决于具体的标准库实现和当时的系统状态。这可能导致性能问题或意外的阻塞。

#### 注意事项

- **显式指定启动策略**：由于默认策略的行为不确定，强烈建议总是显式指定 `std::launch::async` 或 `std::launch::deferred`。`std::launch::async` 是最常用的，因为它能保证真正的并发。
- **`future` 的析构函数行为**：如果由 `std::async` 返回的 `std::future` 在其关联的任务尚未完成时被销毁，该 `future` 的析构函数**会阻塞**，直到任务执行完毕。这是一种安全机制，防止程序在后台线程仍在运行时意外退出，但也可能导致非预期的等待。

    ```cpp
    void might_block() 
    {
        // fut 是局部变量，在函数返回时会被销毁
        // 如果任务还在运行，析构函数会阻塞在此处
        std::future&lt;void&gt; fut = std::async(std::launch::async, []{
            std::this_thread::sleep_for(std::chrono::seconds(5));
        });
    } // &lt;- 此处可能阻塞
    ```

- **参数传递**：传递给 `std::async` 的参数会**被复制或移动**到任务的内部存储中。如果想通过引用传递参数以避免拷贝，并希望在任务中修改原始值，需要使用 `std::ref` 或 `std::cref` 进行包装。

### `std::packaged_task`

`std::packaged_task` 是一个 C&#43;&#43;11 引入的模板类，它将一个可调用对象（函数、lambda 等）包装起来，使其可以被异步调用。它的核心功能是**将任务的定义与任务的执行以及其未来结果分离开来**。

#### 作用

- **解耦任务与线程**：`std::packaged_task` 允许你创建一个“任务包”，这个包内含一个要执行的操作。你可以稍后决定这个任务在哪个线程上执行，甚至可以将其存储在一个队列中，由一个线程池来处理。
- **连接可调用对象与 `std::future`**：它像一个桥梁，一端连接着你的函数代码，另一端通过 `get_future()` 方法提供一个 `std::future` 对象。当这个任务包被执行时，其返回值或异常会自动被存入关联的 `std::future` 中。
- **构建线程池和任务队列**：它是实现线程池、任务调度器等高级并发模式的关键组件。你可以创建一堆 `packaged_task`，把它们放入一个队列，然后让工作线程从队列中取出任务并执行。

#### 用法

基本流程分为三步：包装任务、获取 `future`、执行任务。

```cpp
#include &lt;iostream&gt;
#include &lt;future&gt;
#include &lt;thread&gt;
#include &lt;vector&gt;
#include &lt;functional&gt; // for std::packaged_task

// 任务：计算两个整数的和
int sum(int a, int b) 
{
    std::this_thread::sleep_for(std::chrono::seconds(1));
    return a &#43; b;
}

int main() 
{
    // 1. 包装任务：创建一个 packaged_task，其签名为 int(int, int)
    std::packaged_task&lt;int(int, int)&gt; task(sum);

    // 2. 获取 future：在任务执行前，获取与之关联的 future
    std::future&lt;int&gt; result_future = task.get_future();

    // 3. 执行任务：决定在何处、何时执行任务
    // 这里我们把它移动到一个新线程中执行
    // packaged_task 是不可复制的，只能移动
    std::thread task_thread(std::move(task), 5, 10);

    std::cout &lt;&lt; &#34;Task has been sent to a thread. Waiting for the result...&#34; &lt;&lt; std::endl;

    // 从 future 中获取结果（会阻塞直到任务完成）
    int result = result_future.get();
    std::cout &lt;&lt; &#34;The result is: &#34; &lt;&lt; result &lt;&lt; std::endl;

    task_thread.join(); // 等待线程执行完毕
    return 0;
}
```

#### 常用操作

- **构造函数 `std::packaged_task&lt;Signature&gt;(callable)`**:
  - 用一个可调用对象 `callable` 来创建一个任务包。`Signature` 是函数的签名，例如 `int(int, double)`。
- **`get_future()`**:
  - 返回一个与该任务关联的 `std::future` 对象。**注意：`get_future()` 只能调用一次**。
- **`operator()`**:
  - 执行被包装的任务。调用 `task(args...)` 就会执行内部的可调用对象。任务的返回值（或异常）会被自动存入 `future`。
- **`make_ready_at_thread_exit()`**:
  - 在任务执行后，调用此方法可以使任务的共享状态在线程退出时才变为 `ready`。这在处理线程局部变量（thread-local variables）时非常有用。
- **`reset()`**:
  - 重置 `packaged_task` 的状态，使其可以被再次执行。它会释放之前的结果，并重新与一个新的 `std::future` 关联（需要再次调用 `get_future()`）。

#### 注意事项

- **移动语义（Move-Only）**：`std::packaged_task` 是一个只移类型（Move-Only），它不可复制。这意味着你不能将其拷贝到多个地方，只能通过 `std::move` 来转移其所有权，例如将其存入容器或传递给线程。
- **一次性的 `get_future()`**：与 `std::promise` 类似，一个 `packaged_task` 实例的 `get_future()` 方法只能被成功调用一次。再次调用会抛出 `std::future_error`。

### `std::future`

`std::future` 提供了一种访问**异步操作结果**的机制。当你启动一个异步任务时（例如，通过 `std::async`，`std::packaged_task` 或 `std::promise`），你会得到一个 `std::future` 对象。这个 `future` 对象在未来的某个时刻会持有该异步任务的计算结果或抛出的异常。

#### 作用

- **获取异步结果**：主线程可以随时通过 `future` 对象查询异步任务是否完成，并获取其返回值，而无需自己手动管理线程和共享状态。
- **线程同步**：`future` 的 `.get()` 或 `.wait()` 方法会阻塞当前线程，直到异步任务完成。这是一种简单的线程同步方式。
- **异常传递**：如果异步任务在执行过程中抛出异常，该异常会被捕获并存储在 `future` 对象中。当主线程调用 `.get()` 时，该异常会被重新抛出。

#### 用法

最常见的用法是与 `std::async` 配合使用：

```cpp
#include &lt;iostream&gt;
#include &lt;future&gt;
#include &lt;thread&gt;
#include &lt;chrono&gt;

// 一个可能耗时较长的计算任务
int calculate_something() 
{
    std::this_thread::sleep_for(std::chrono::seconds(2));
    if (/* some error condition */ false) 
    {
        throw std::runtime_error(&#34;Calculation failed!&#34;);
    }
    
    return 123;
}

int main() 
{
    // 使用 std::async 启动一个异步任务
    // std::launch::async 策略保证任务在一个新线程上运行
    std::future&lt;int&gt; fut = std::async(std::launch::async, calculate_something);

    std::cout &lt;&lt; &#34;Doing other work in main thread...&#34; &lt;&lt; std::endl;

    // ... 在主线程中可以做其他事情 ...

    std::cout &lt;&lt; &#34;Waiting for the result...&#34; &lt;&lt; std::endl;
    try {
        // 调用 .get() 来获取结果
        // 如果任务尚未完成，此调用会阻塞当前线程
        int result = fut.get();
        std::cout &lt;&lt; &#34;The result is: &#34; &lt;&lt; result &lt;&lt; std::endl;
    } catch (const std::exception&amp; e) {
        std::cout &lt;&lt; &#34;Exception from async task: &#34; &lt;&lt; e.what() &lt;&lt; std::endl;
    }

    return 0;
}
```

#### 常用操作

- `get()`: 等待异步任务完成并返回其结果。如果任务抛出异常，`get()` 会重新抛出该异常。**注意：`get()` 只能调用一次**。
- `wait()`: 等待异步任务完成，但不获取结果。
- `wait_for()` / `wait_until()`: 等待一段时间或直到某个时间点。
- `valid()`: 检查 `future` 对象是否与一个共享状态关联。调用 `get()` 后，`future` 会变为 invalid。

#### 注意事项

- **`get()` 的一次性**：`std::future` 的 `get()` 成员函数只能被调用一次。因为结果（或异常）可能会被移动出来，而不是复制。再次调用会抛出异常。如果需要多次访问结果，应该使用 `std::shared_future`。
- **`std::async` 的析构行为**：如果一个由 `std::async` 返回的 `std::future` 对象在析构时，其关联的异步任务还未完成，那么这个析构函数会阻塞，直到任务完成。这被称为“延迟销毁”，有时会造成意想不到的阻塞。

### `std::invoke_result_t`

`std::invoke_result_t` 是 C&#43;&#43;17 中引入的类型别名，用于在**编译期**推断一个可调用对象（函数、函数指针、lambda 表达式、成员函数指针、函数对象等）在以特定参数调用时，其返回值的类型。它是 `std::invoke_result&lt;F, Args...&gt;::type` 的简写形式。

#### 作用

- **编译期类型推导**：它允许你在编写模板或泛型代码时，能够提前知道一个函数调用将返回什么类型，而无需实际执行该调用。
- **SFINAE 和元编程**：在模板元编程中，它可以用于根据函数返回类型进行 SFINAE（Substitution Failure Is Not An Error，替换失败并非错误）判断，或者定义依赖于函数返回类型的变量和数据结构。

#### 用法

```cpp
#include &lt;iostream&gt;
#include &lt;type_traits&gt;
#include &lt;vector&gt;
#include &lt;string&gt;

int add(int a, int b) 
{
    return a &#43; b;
}

struct Foo 
{
    double bar(float) { return 1.0; }
};

int main() 
{
    // 推断普通函数调用的返回类型
    using Result1 = std::invoke_result_t&lt;decltype(add), int, int&gt;;
    static_assert(std::is_same_v&lt;Result1, int&gt;);

    // 推断 lambda 表达式的返回类型
    auto lambda = [](const std::string&amp; s) -&gt; std::vector&lt;char&gt; {
        return {s.begin(), s.end()};
    };
    using Result2 = std::invoke_result_t&lt;decltype(lambda), std::string&gt;;
    static_assert(std::is_same_v&lt;Result2, std::vector&lt;char&gt;&gt;);

    // 推断成员函数指针的返回类型
    // 注意：推导成员函数指针返回类型时，第一个参数应为能绑定该成员函数的对象类型，
    // 如 T&amp;, T*, 或 std::reference_wrapper&lt;T&gt;
    using Result3 = std::invoke_result_t&lt;decltype(&amp;Foo::bar), Foo&amp;, float&gt;;
    static_assert(std::is_same_v&lt;Result3, double&gt;);

    std::cout &lt;&lt; &#34;All types deduced correctly.&#34; &lt;&lt; std::endl;

    return 0;
}
```

#### 注意事项

- **非静态成员函数**：对于非静态的成员函数，`invoke_result_t`只能使用其函数指针才能进行返回值类型推导，因为`类名::成员函数名`不是完整的成员限定表达式，只有加了`&amp;`成为成员函数指针，才能脱离类定义被使用。
- **C&#43;&#43;17标准**：`std::invoke_result_t` 是 C&#43;&#43;17 的特性，它在处理某些复杂情况（如成员函数指针）时不如 `std::invoke_result` 强大且已被 C&#43;&#43;20 废弃。

### `std::move`

`std::move` 是一个标准库函数，它的主要作用是**声明**一个对象可以被“移动”，即其资源可以被窃取。它本身并不执行任何移动操作，真正的移动操作是由移动构造函数或移动赋值运算符完成的。

#### 作用

- **启用移动语义**：将左值（有名字、可以取地址的对象）转换为右值，从而使得可以调用对象的移动构造函数或移动赋值运算符，避免不必要的深拷贝，提升性能。
- **转移所有权**：对于像 `std::unique_ptr`、`std::thread` 这种只移类型（Move-Only Type），`std::move` 是转移其所有权的唯一方式。

#### 用法

```cpp
#include &lt;iostream&gt;
#include &lt;string&gt;
#include &lt;vector&gt;
#include &lt;utility&gt; // for std::move

int main() 
{
    std::string str1 = &#34;hello&#34;;
    std::string str2 = &#34;world&#34;;

    std::cout &lt;&lt; &#34;Before move:&#34; &lt;&lt; std::endl;
    std::cout &lt;&lt; &#34;str1: &#34; &lt;&lt; str1 &lt;&lt; std::endl;
    std::cout &lt;&lt; &#34;str2: &#34; &lt;&lt; str2 &lt;&lt; std::endl;

    // 这里，std::move(str1) 将左值 str1 转换为右值引用
    // 这使得 std::string 的移动赋值运算符被调用
    str2 = std::move(str1);

    std::cout &lt;&lt; &#34;\nAfter move:&#34; &lt;&lt; std::endl;
    std::cout &lt;&lt; &#34;str1: &#34; &lt;&lt; str1 &lt;&lt; std::endl; // str1 处于有效的、但未指定的状态
    std::cout &lt;&lt; &#34;str2: &#34; &lt;&lt; str2 &lt;&lt; std::endl; // str2 获得了 str1 的资源

    std::vector&lt;int&gt; vec1 = {1, 2, 3, 4, 5};
    // 调用 vector 的移动构造函数
    std::vector&lt;int&gt; vec2 = std::move(vec1);

    std::cout &lt;&lt; &#34;\nVector size after move:&#34; &lt;&lt; std::endl;
    std::cout &lt;&lt; &#34;vec1 size: &#34; &lt;&lt; vec1.size() &lt;&lt; std::endl; // 通常为空
    std::cout &lt;&lt; &#34;vec2 size: &#34; &lt;&lt; vec2.size() &lt;&lt; std::endl; // 5

    return 0;
}
```

#### 注意事项

- **被移动后的对象状态**：使用 `std::move` 后，原对象处于“有效的、但未指定的状态”（valid but unspecified state）。这意味着你不能对其状态做任何假设（例如，它可能为空，也可能不是），但你可以安全地对其进行销毁或重新赋值。在对其重新赋值之前，不应再使用它。
- **不要对 `const` 对象使用 `std::move`**：对 `const` 对象 `std::move` 会得到一个 `const` 类型的右值引用，这通常会退化为一次拷贝操作，因为 `const` 对象是不可修改的，其资源无法被移动。
- **仅在需要转移所有权时使用**：`std::move` 的名字有一定误导性，它更像是一个“转换”或“允许移动”的请求。只有当你确定不再需要原对象，并希望将其资源转移给新对象时，才应该使用它。

### `std::forward`

`std::forward` 是一个条件转换函数，主要用于模板编程中的“完美转发”（Perfect Forwarding）。它根据模板参数的类型（左值引用或右值引用），将函数参数以原始的值类别（左值或右值）转发给另一个函数。

#### 作用

- **保持值类别**：在模板函数中，函数参数会丢失其原始的值类别信息（都变成了左值）。`std::forward` 的作用是恢复这个信息，如果原始传入的是右值，就转发为右值；如果原始传入的是左值，就转发为左值。
- **实现完美转发**：这使得我们可以编写一个模板函数作为“中转站”，它能接收任意类型的参数，并将其无损地（不改变值类别，不产生额外拷贝）传递给下一个函数。

#### 用法

它通常用在接受转发引用（Forwarding Reference，也叫万能引用 Universal Reference）的模板函数中。转发引用是一种特殊的模板参数形式 `T&amp;&amp;`，其中 `T` 是一个需要推导的类型。

```cpp
#include &lt;iostream&gt;
#include &lt;utility&gt; // for std::forward

// 目标函数，有针对左值引用和右值引用的重载
void overloaded_func(int&amp; x) 
{
    std::cout &lt;&lt; &#34;Called with lvalue reference&#34; &lt;&lt; std::endl;
}

void overloaded_func(int&amp;&amp; x) 
{
    std::cout &lt;&lt; &#34;Called with rvalue reference&#34; &lt;&lt; std::endl;
}

// 一个“转发”或“包装”的模板函数
template&lt;typename T&gt;
void wrapper(T&amp;&amp; arg) 
{
    // 在这里，不管 arg 原始是左值还是右值，它本身都是一个有名字的左值
    // 如果直接调用 overloaded_func(arg)，永远都会调用左值版本
    
    // 使用 std::forward 来保持原始的值类别
    overloaded_func(std::forward&lt;T&gt;(arg));
}

int main() 
{
    int i = 42;
    wrapper(i);         // i 是左值，T 被推导为 int&amp;，wrapper 转发一个左值
    wrapper(100);       // 100 是右值，T 被推导为 int，wrapper 转发一个右值
    wrapper(std::move(i)); // std::move(i) 是右值，T 被推导为 int，wrapper 转发一个右值

    return 0;
}
```

上面的示例中，T的类型被推导出来后，`wrapper`函数的参数就为 **推导的类型 &#43; `&amp;&amp;`**，这时会发生引用坍缩，参数类型会变为正确的类型。

#### 注意事项

- **必须与转发引用配合**：`std::forward` 只应在接受转发引用（`T&amp;&amp;`）的上下文中使用。如果用在普通的值传递或非转发引用的参数上，行为可能不符合预期。
- **模板参数 `T` 是关键**：`std::forward&lt;T&gt;` 的模板参数 `T` 必须是函数模板推导出的类型，它包含了原始参数是左值还是右值的信息。

### `std::apply`

`std::apply` 是 C&#43;&#43;17 引入的函数，它允许你使用一个元组（`std::tuple`）或类似元组的对象（如 `std::pair`, `std::array`）的元素作为参数来调用一个可调用对象。

#### 作用

- **解包元组**：将一个元组中的元素“解开”，并按顺序作为独立的参数传递给一个函数。这在泛型编程和处理可变参数模板时非常有用。
- **简化函数调用**：当你需要调用的函数的参数被存储在一个元组中时，`std::apply` 提供了一种简洁、优雅的方式来执行调用，而无需手动索引元组的每个元素。

#### 用法

```cpp
#include &lt;iostream&gt;
#include &lt;tuple&gt;
#include &lt;functional&gt; // for std::apply

void print_sum(int a, int b, int c) 
{
    std::cout &lt;&lt; a &lt;&lt; &#34; &#43; &#34; &lt;&lt; b &lt;&lt; &#34; &#43; &#34; &lt;&lt; c &lt;&lt; &#34; = &#34; &lt;&lt; a &#43; b &#43; c &lt;&lt; std::endl;
}

struct MyStruct 
{
    void process(const std::string&amp; name, int id) 
    {
        std::cout &lt;&lt; &#34;Processing &#34; &lt;&lt; name &lt;&lt; &#34; with ID &#34; &lt;&lt; id &lt;&lt; std::endl;
    }
};

int main() 
{
    // 1. 调用普通函数
    std::tuple&lt;int, int, int&gt; args = {10, 20, 30};
    std::apply(print_sum, args); // 等价于 print_sum(10, 20, 30)

    // 2. 调用 lambda 表达式
    auto lambda = [](double x, double y) { std::cout &lt;&lt; &#34;Product: &#34; &lt;&lt; x * y &lt;&lt; std::endl; };
    std::apply(lambda, std::make_pair(3.0, 4.0)); // 使用 std::pair

    // 3. 调用成员函数
    MyStruct obj;
    std::tuple&lt;std::string, int&gt; member_args = {&#34;Widget&#34;, 123};
    std::apply([&amp;obj](const auto&amp;... params){ obj.process(params...); }, member_args);
    // 或者更直接的方式（需要 C&#43;&#43;17 的扩展）
    // std::apply(&amp;MyStruct::process, std::tuple_cat(std::make_tuple(&amp;obj), member_args));

    return 0;
}
```

#### 注意事项

- **C&#43;&#43;17 标准**：`std::apply` 是 C&#43;&#43;17 的特性。在之前的版本中，需要手动编写复杂的模板元编程代码（通常涉及索引序列 `std::index_sequence`）来实现类似的功能。
- **参数顺序**：元组中的元素会严格按照从 0 开始的索引顺序，依次映射到函数的第一个、第二个、第三个...参数。
- **兼容性**：任何支持 `std::get` 和 `std::tuple_size` 的类（Tuple-like-object）都可以与 `std::apply` 一起使用。这包括 `std::tuple`, `std::pair` 和 `std::array`。

### 互斥量与锁包装器

在C&#43;&#43;17标准下，常用的互斥量如下所示：

| 互斥锁类型                   | 简介                                                                                 | 使用场景                                                         |
| ---------------------------- | ------------------------------------------------------------------------------------ | ---------------------------------------------------------------- |
| `std::mutex`                 | 最基本的互斥锁，不能递归锁定，一个线程加锁后，其他任何线程（包括自己）都不能再次加锁 | 通用互斥锁，保护绝大多数常规的临界区                             |
| `std::recursive_mutex`       | 允许同一个线程多次加锁的互斥锁，需手动解锁相同次数后，其他线程才能获取               | 需要在递归函数或同一线程中多次加锁的场景                         |
| `std::timed_mutex`           | 带计时的互斥锁，超时自动解锁                                                         | 当你不希望线程无限期地等待一个锁时，如线程池任务调度、定时任务等 |
| `std::recursive_timed_mutex` | 结合递归锁和超时锁的特性的互斥锁                                                     | 既需要递归锁定又要求支持超时的场景，如复杂嵌套逻辑下的任务控制   |
| `std::shared_mutex`          | 读写锁，多个线程可共享读，写互斥                                                     | 读多写少场景，如缓存、配置、数据库读接口等                       |

直接调用锁的`lock()` 和 `unlock()` 函数是危险的，因为异常或复杂的逻辑分支可能导致 `unlock()` 被跳过。因此，推荐使用锁包装器来管理锁，锁包装器采用RAII机制，能够确保安全的使用锁。常用的锁包装器有以下几种：

#### `std::lock_guard`

最简单、高效的锁包装器。

- **工作方式**: 在构造时，它接收一个或多个互斥量（C&#43;&#43;17 开始支持多个）并立即对它们加锁。在析构时（离开作用域），它会自动解锁。
- **特点**:
  - **简单粗暴**: 一旦创建就加锁，没有其他多余操作。
  - **不可移动，不可复制**: 它的所有权与作用域绑定。
  - **无额外开销**: 通常会被编译器优化掉，性能与手动 `lock/unlock` 几乎无异，但更安全。
  - **独占访问**: 可以确保某一时刻只有一个线程可以访问被保护的共享资源。
- **适用场景**: 当你需要在一个完整的作用域内锁定一个或多个互斥量，且不需要任何高级的锁操作时。这是最常用的选择。

```cpp
#include &lt;mutex&gt;
std::mutex mtx;

void some_function() 
{
    // 构造时加锁，函数返回时（lock析构）自动解锁
    std::lock_guard&lt;std::mutex&gt; lock(mtx);
    // ... 安全地访问共享资源 ...
    // 不需要手动 unlock
}
```

#### `std::unique_lock`

最灵活的锁包装器，相比与`std::lock_guard`提供了更多的灵活性，但也是独占访问。

- **工作方式**: 提供了对互斥量所有权的完全控制。
- **特点**:
  - **所有权管理**: `std::unique_lock` 对象拥有其管理的互斥量的锁。这个所有权可以被转移（通过移动构造或移动赋值），也可以被临时释放和重新获取。
  - **可移动，不可复制**: 可以作为函数返回值，或存入容器中。
  - **支持延迟加锁**: 可以在构造时不加锁（使用 `std::defer_lock`），稍后手动调用 `lock()`。
  - **支持尝试加锁**: 可以使用 `try_lock()`、`try_lock_for()`、`try_lock_until()`。
  - **支持所有权转移**: `release()` 方法会返回底层互斥量的指针并放弃所有权，但不会解锁。`std::move` 可以将所有权转移给另一个 `unique_lock`。
- **适用场景**:
  - **与条件变量（`std::condition_variable`）配合使用**：这是 `unique_lock` 最核心的用途。条件变量的 `wait` 系列函数必须接收一个 `std::unique_lock`。
  - **需要提前解锁**: 在临界区结束前，如果某些操作不再需要锁的保护，可以调用 `unlock()` 提前释放锁。
  - **锁的作用域不固定**: 当锁的生命周期需要跨越多个作用域或在函数间传递时。

```cpp
#include &lt;mutex&gt;
#include &lt;condition_variable&gt;

std::mutex mtx;
std::condition_variable cv;
bool ready = false;

void worker_thread() 
{
    // 构造时加锁
    std::unique_lock&lt;std::mutex&gt; lock(mtx);
    // cv.wait 会原子地：1. 解锁 a. 2. 阻塞线程 3. 被唤醒后，重新加锁
    cv.wait(lock, []{ return ready; });
    // ... 当 wait 返回时，锁已被重新获取 ...
    // 可以在这里提前解锁
    lock.unlock();
    // ... 执行一些不需要锁的操作 ...
}
```

#### `std::shared_lock`

`std::shared_mutex` 的共享模式（读模式）锁包装器。

- **工作方式**: 类似于 `unique_lock`，但它在构造时获取的是共享锁。
- **特点**:
  - 与 `unique_lock` 类似，它也是可移动、可延迟加锁、可尝试加锁的。
  - 与 `std::unique_lock&lt;std::shared_mutex&gt;` (写锁) 配合使用。
- **适用场景**: 用于 `std::shared_mutex` 的读锁定。

```cpp
#include &lt;shared_mutex&gt;
#include &lt;vector&gt;

class ThreadSafeData 
{
private:
    mutable std::shared_mutex mutex_; // mutable 允许在 const 成员函数中加锁
    std::vector&lt;int&gt; data_;

public:
    int read_data(size_t index) const 
    {
        // 获取共享锁（读锁），多个线程可以同时进入这里
        std::shared_lock&lt;std::shared_mutex&gt; lock(mutex_);
        return data_[index];
    }

    void write_data(int value)
    {
        // 获取独占锁（写锁），当此锁被持有时，任何其他读/写操作都会被阻塞
        std::unique_lock&lt;std::shared_mutex&gt; lock(mutex_);
        data_.push_back(value);
    }
};
```

#### `std::scoped_lock`

`std::lock_guard` 的终极进化版，也是 C&#43;&#43;17 中处理多互斥量锁定的最佳实践。

- **工作方式**: 这是一个可变参数模板，可以同时接收任意数量的互斥量，并以**避免死锁的方式**将它们全部锁定。
- **特点**:
  - **死锁避免算法**: 它内部使用了一种死锁避免算法（如 `std::lock` 函数），确保在锁定多个互斥量时不会因为加锁顺序不同而导致死锁。
  - **RAII 封装**: 与 `lock_guard` 一样，它在构造时加锁，析构时按加锁相反的顺序解锁。
  - **语法简洁**: 是 C&#43;&#43;17 中锁定多个互斥量的首选方式。
- **适用场景**: 当你需要同时锁定**两个或更多**互斥量时。

### 条件变量

互斥量解决了“访问”的互斥问题，确保同一时间只有一个线程能进入临界区。但它无法解决“等待”的同步问题，例如：

- **生产者-消费者问题**：当队列为空时，消费者线程如何高效地等待生产者放入数据，而不是通过一个 `while(true)` 循环不停地检查队列（这被称为“忙等待”，会浪费大量 CPU 资源）？
- **任务完成通知**：一个主线程如何等待多个工作线程全部完成其初始化工作后，再继续执行？

条件变量正是为了解决这类问题而生的。

#### 核心作用

1. **线程阻塞与唤醒**: 允许线程在某个条件不满足时，原子地释放互斥锁并进入阻塞（睡眠）状态，从而避免了“忙等待”。当其他线程改变了该条件后，可以发送通知来唤醒等待的线程。
2. **线程间的同步信令**: 作为一种线程间通信机制，用于同步执行流程，一个线程等待（`wait`），另一个线程通知（`notify`）。

C&#43;&#43; 标准库提供了两种条件变量：

1. **`std::condition_variable`**: 最高效，但它**必须**与 `std::unique_lock&lt;std::mutex&gt;` 配合使用。这是最常用的条件变量。
2. **`std::condition_variable_any`**: 更通用，可以与任何满足 `BasicLockable` 要求的锁类型配合（例如 `std::shared_lock`），但可能会有额外的性能开销。除非有特殊需求，否则应优先使用 `std::condition_variable`。

#### 工作流程

条件变量的典型工作流程如下，以一个等待线程（消费者）和一个通知线程（生产者）为例：

**等待线程 (Waiting Thread):**

1. 获取 `std::mutex` 的 `std::unique_lock`。
2. **检查条件**。通常在一个循环中（`while` 或 `if`）检查某个共享状态（如 `queue.empty()`）。
3. 如果条件**不满足**： a. 调用 `cv.wait(lock)`。这个调用会**原子地**做三件事：
    i. **解锁**互斥量 `lock`。
    ii. **阻塞**当前线程，使其进入睡眠状态。
    iii. （当被唤醒后）**重新加锁**互斥量 `lock`，然后 `wait` 函数返回。 b. `wait` 返回后，循环会**再次检查条件**。
4. 如果条件**满足**：线程跳出循环，继续持有锁并执行后续操作（如从队列中取数据）。
5. 在离开作用域时，`unique_lock` 的析构函数会自动解锁互斥量。

**通知线程 (Notifying Thread):**

1. 获取与等待线程相同的 `std::mutex` 的 `std::lock_guard` 或 `std::unique_lock`。
2. **修改共享状态**，使得等待线程的条件得以满足（如向队列中添加数据）。
3. （可选但推荐）在修改完共享状态后，并且在解锁互斥量**之前**，调用 `cv.notify_one()` 或 `cv.notify_all()` 来唤醒一个或所有正在等待的线程。
4. 离开作用域，锁被释放。

#### `std::condition_variable` 主要成员函数

- **`wait(std::unique_lock&lt;std::mutex&gt;&amp; lock)`**: 使当前线程阻塞，直到被 `notify`。如上所述，它会自动释放锁并在此后重新获取。
- **`wait(std::unique_lock&lt;std::mutex&gt;&amp; lock, Predicate pred)`**: 这是一个重载版本，是**强烈推荐**的使用方式，可以有效避免虚假唤醒。`pred`一个可以调用的对象或者函数，函数或者对象没有参数并且需要返回一个bool类型的值，线程将会不停的调用`wait`函数直到该返回值为`true`。该版本的行为分为两部分：
  - **初次进入 wait() 时**：
    - 自动调用 `predicate()`；
    - 若返回 `true`，**直接跳出，不阻塞**；
    - 若返回 `false`，释放锁并阻塞线程（让出 CPU）。
  - **线程被唤醒后（收到 notify）**：
    - **先重新获得锁**；
    - 然后再次调用 `predicate()` 判断；
    - 如果为 `true`：继续执行；
    - 如果为 `false`：继续阻塞（避免虚假唤醒）；

```cpp
std::mutex mtx;
std::condition_variable cv;
bool isReady;

// 下面两种写法是等价的

//写法一
while(true) {
    sleep(1);
    std::unique_lock&lt;std::mutext&gt; lk(mtx);
    while (!isReady) {
        cv.wait(lk);
    }
}
//写法二
while (true) {
    sleep(1);
    std::unique_lock&lt;std::mutext&gt; lk(mtx);
    cv.wait(lk, []{return isReady;});
}
```

- **`wait_for(lock, duration)` / `wait_for(lock, duration, pred)`**: 带超时的等待。如果在指定的 `duration` 时间段内没有被唤醒（或 `pred` 未满足），`wait` 也会返回。可以通过其返回值判断是正常唤醒还是超时返回。
- **`wait_until(lock, time_point)` / `wait_until(lock, time_point, pred)`**: 与 `wait_for` 类似，但等待直到一个指定的时间点 `time_point`。
- **`notify_one()`**: 唤醒**一个**正在等待的线程。如果有多个线程在等待，操作系统会选择其中一个来唤醒。如果没有线程在等待，此调用无效。
- **`notify_all()`**: 唤醒**所有**正在等待的线程。这些被唤醒的线程会竞争同一个互斥锁，最终只有一个能立即执行，其他线程会继续阻塞在锁上。

## 设计概览

```mermaid
classDiagram
    namespace 线程池模块 {
        class ThreadPool {
            -string m_name
            -atomic~Status~ m_status
            -mutex m_status_mutex
            -queue~Task~ m_task_queue
            -mutex m_task_queue_mutex
            -condition_variable m_task_queue_cv
            -list~WorkerThread~ m_worker_list
            -list~WorkerThread~ m_zombie_workers
            -thread m_monitor_thread
            &#43;explicit ThreadPool(const ThreadPoolConfig&amp; config)
            &#43;~ThreadPool()
            &#43;pause()
            &#43;resume()
            &#43;shutdown()
            &#43;increaseThreadCount(size_t count)
            &#43;decreaseThreadCount(size_t count)
            &#43;setMaxTaskCount(size_t count)
            &#43;getThreadCount() : size_t
            &#43;getStatus() : string
            &#43;getThreadPoolConfig() : ThreadPoolConfig
            &#43;submit(Task) : future~Result~
            -monitorLoop()
            -adjustThreadCount()
        }

        class WorkerThread {
            -ThreadPool* m_pool_ptr
            -atomic~Status~ m_status
            -BinarySemaphore m_pause_sem
            -thread m_thread
            -atomic~time_point~ m_last_active_time
            &#43;explicit WorkerThread(ThreadPool* pool_ptr)
            &#43;~WorkerThread()
            &#43;terminate()
            &#43;pause()
            &#43;resume()
            &#43;getLastActiveTime() : time_point
            -isWake() : bool
            -run()
        }

        class ThreadPoolConfig {
            &#43;size_t max_task_count
            &#43;size_t core_thread_count
            &#43;size_t max_thread_count
            &#43;milliseconds keep_alive_time
            &#43;milliseconds monitor_interval
            &#43;bool enable_dynamic_scaling
            &#43;operator_equals(const ThreadPoolConfig&amp; oth) : bool
        }

        class BinarySemaphore {
            -mutex m_mutex
            -condition_variable m_cv
            -bool m_flag
            &#43;explicit BinarySemaphore(bool initially_available)
            &#43;acquire()
            &#43;release()
        }
    }

    ThreadPool &#34;1&#34; *-- &#34;N&#34; WorkerThread : contains
    ThreadPool ..&gt; ThreadPoolConfig : uses
    WorkerThread ..&gt; ThreadPool : uses
    WorkerThread &#34;1&#34; *-- &#34;1&#34; BinarySemaphore : contains
```

### 库架构

线程池采用了模块化设计，主要由下面三个核心组件构成：

1. **`ThreadPool` 类**：作为用户直接交互的接口，负责任务调度、线程管理等核心功能。用户通过创建 `ThreadPool` 实例来提交任务、控制线程池状态、并获取线程池相关信息。
2. **`ThreadPool::WorkerThread` 类**：作为线程池内部的工作单元，每个 `ThreadPool::WorkerThread` 对象代表一个独立的工作线程，负责从任务队列中取出任务并执行。
3. **辅助工具**：包括同步原语（如互斥锁、条件变量、信号量等）以及状态管理机制，它们为线程池和工作线程之间的通信、任务同步、状态变更等操作提供了必要的支撑。

各组件间的关系如下：

- `ThreadPool` 类维护一个工作线程列表 `std::list&lt;WorkerThread&gt;`，并通过同步原语控制任务队列的访问与状态变更。并通过`ThreadPool::WorkerThread`提供的接口，对工作线程发出各种指令（如暂停、恢复、终止等）。
- `ThreadPool::WorkerThread` 类定义一系列接口供 `ThreadPool` 类使用，从而能够获取待执行任务、更新自身状态。
- 辅助工具贯穿于整个库的设计与实现中，确保并发环境下的数据一致性与操作安全性。

### 状态转换

线程池的状态转换如下所示：

```mermaid
stateDiagram-v2
    direction LR
    [*] --&gt; RUNNING: 初始化

    RUNNING --&gt; PAUSED: 调用 pause()
    PAUSED --&gt; RUNNING: 调用 resume()

    RUNNING --&gt; SHUTDOWN: 调用 shutdown()
    PAUSED --&gt; RUNNING: (1)shutdown()内部先调用resumeWithoutStatusLock()
    RUNNING --&gt; SHUTDOWN: (2)再自动进入

    SHUTDOWN --&gt; TERMINATING: 任务队列为空
    TERMINATING --&gt; TERMINATED: 全部工作线程 join

    TERMINATED --&gt; [*]
```

工作线程的状态转换如下所示：

```mermaid
stateDiagram-v2
    direction LR
    [*] --&gt; RUNNING: 初始化

    RUNNING --&gt; PAUSED: 调用 pause()
    PAUSED --&gt; RUNNING: 调用 resume()

    RUNNING --&gt; TERMINATING: 调用 terminate()
    PAUSED --&gt; TERMINATING: 调用 terminate()

    TERMINATING --&gt; TERMINATED: run() 循环结束
    TERMINATED --&gt; [*]
```

### 线程数量增减

本线程池实现了两种方式的线程池数量增减，第一种为**用户手动**调用`increaseThreadCount`和`decreaseThreadCount`函数来进行线程数量的增减：

```mermaid
sequenceDiagram
    actor User as 用户
    participant TP as ThreadPool
    participant WT as WorkerThread

    User-&gt;&gt;TP: increaseThreadCount(N)
    activate TP
    TP-&gt;&gt;TP: 1. 锁定状态锁 (m_status_mutex)
    TP-&gt;&gt;TP: 2. 检查状态是否为 RUNNING/PAUSED
    loop N 次
        TP-&gt;&gt;WT: 3. new WorkerThread(this)
        activate WT
        note right of WT: 构造函数启动 std::thread, &lt;br/&gt;线程进入 RUNNING 状态
        WT--&gt;&gt;TP: 构造完成
        deactivate WT
    end
    TP-&gt;&gt;TP: 4. 解除状态锁
    deactivate TP
    User-&gt;&gt;TP: decreaseThreadCount(N)
    activate TP
    TP-&gt;&gt;TP: 1. 锁定状态锁 (m_status_mutex)
    TP-&gt;&gt;TP: 2. 检查状态是否为 RUNNING/PAUSED
    loop N 次
        TP-&gt;&gt;WT: 3. worker.terminate()
        activate WT
        note right of WT: 状态变为 TERMINATING, &lt;br/&gt;并唤醒自己准备退出
        WT--&gt;&gt;TP: terminate() 返回
        deactivate WT
        TP-&gt;&gt;TP: 4. 将 WorkerThread 移入僵尸列表
    end
    TP-&gt;&gt;TP: 5. 解除状态锁
    deactivate TP
```

第二种为启用监控线程（需要在初始化线程池时指定参数`enable_dynamic_scaling`参数为`true`），监控线程将会根据线程池的负载**自动增减**工作线程：

```mermaid
graph TD
    subgraph MonitorThread [监控线程: monitorLoop]
        A(启动监控线程) --&gt; B(循环开始);

        B --&gt; C{&#34;m_terminating_flag为true?&#34;};
        C -- 是 --&gt; Z([线程终止]);
        C -- 否 --&gt; D[等待monitor_interval时间或被唤醒];
        
        D --&gt; F{&#34;should_terminate为true?&#34;};
        F -- 是 --&gt; Z;
        F -- 否 --&gt; I[&#34;调用adjustThreadCount()&#34;];
        
        I --&gt; B;
    end

    subgraph adjustThreadCount [调整线程数逻辑]
        I --&gt; J[获取线程池状态锁];
        J --&gt; K{&#34;当前状态是RUNNING或PAUSED?&#34;};
        K -- 否 --&gt; L[释放status_lock锁];
        L --&gt; M(调整结束);
        
        K -- 是 --&gt; N[获取相关信息];
        
        N --&gt; O{&#34;满足扩容条件?&#34;};
        O -- 是 --&gt; P[&#34;调用increaseThreadCountWithoutStatusLock(1)&#34;];
        P --&gt; L;
        
        O -- 否 --&gt; Q{&#34;满足缩容条件?&#34;};
        Q -- 否 --&gt; L;
        
        Q -- 是 --&gt; R[计算超时空闲线程数];
        
        R --&gt; W{&#34;超时空闲线程数 &gt; 0?&#34;};
        W -- 是 --&gt; X[&#34;调用decreaseThreadCountWithoutStatusLock(N)&#34;];
        X --&gt; L;
        W -- 否 --&gt; L;
    end
```

### 任务调度与执行

完整的任务生命周期如下所示：

```mermaid
sequenceDiagram
    actor Client as &#34;任务提交者&#34;
    participant TP as &#34;ThreadPool&#34;
    participant TQ as &#34;任务队列&#34;
    participant Future as &#34;std::future&#34;
    participant WT as &#34;WorkerThread (空闲)&#34;

    Client-&gt;&gt;TP: submit(function, args...)
    activate TP
    TP-&gt;&gt;TP: 1. 封装为 packaged_task
    TP-&gt;&gt;Future: 2. task.get_future()
    activate Future
    Future--&gt;&gt;TP: future 对象
    deactivate Future
    TP-&gt;&gt;TP: 3. 再次封装为 std::function&amp;lt;void()&amp;gt;
    TP-&gt;&gt;TQ: 4. 将任务加入任务队列
    deactivate TP
    
    TP-&gt;&gt;WT: 5. m_task_queue_cv.notify_one()
    TP--&gt;&gt;Client: 6. 返回 future 对象

    activate WT
    WT-&gt;&gt;TQ: 7. lock(), task = task_queue.front()
    activate TQ
    TQ--&gt;&gt;WT: task
    WT-&gt;&gt;TQ: 8. task_queue.pop(), unlock()
    deactivate TQ
    WT-&gt;&gt;WT: 9. 执行任务
    note over WT, Future: 任务结果/异常被存入&lt;br/&gt;与 future 关联的共享状态中
    deactivate WT

    Client-&gt;&gt;Future: get() [阻塞等待结果]
    activate Future
    Future--&gt;&gt;Client: 返回任务结果或抛出异常
    deactivate Future
```

## 主要实现

### `submit`函数

线程池设计的基本理念就是：**任务入队时就应该已经具备所有执行信息（函数体 &#43; 参数）**，工作线程只负责调用。

```cpp
    /**
     * @brief 提交任务到任务队列, 并返回一个 future 对象以获取执行结果
     * @details 支持任意函数签名及参数组合. 内部将任务封装为无参的 `std::function&lt;void()&gt;`
     *          形式, 统一工作线程的任务调度格式. 任务将在后台线程异步执行.
     *          暂停态也可以提交任务, 但不会执行新任务
     *
     * @note 如果某个参数是引用类型, 则在提交任务时要用 std::ref(data) 的形式来明确告知submit函数传引用
     *
     * @tparam F 可调用对象的类型(函数指针, lambda, std::function等)
     * @tparam Args 可调用对象 f 所需的参数类型
     *
     * @param[in] f 要执行的函数对象
     * @param[in] args 要传递给函数 f 的参数(可变参数包)
     * @return 返回一个 future 对象
     */
    template&lt;typename F, typename... Args&gt;
    std::future&lt;std::invoke_result_t&lt;F, Args...&gt;&gt; submit(F&amp;&amp; f, Args&amp;&amp;... args)
    {
      // 加锁, 防止在提交任务过程中线程池状态发生变化
      std::lock_guard&lt;std::mutex&gt; status_lock(m_status_mutex);
      const Status current_status = m_status.load(std::memory_order_acquire);

      // 检查当前线程池状态
      if (current_status != Status::RUNNING &amp;&amp; current_status != Status::PAUSED)
      {
        VELOX_ERROR(&#34;[submit error]: ThreadPool is in a state where it cannot submit tasks&#34;);
        throw std::runtime_error(&#34;[submit error]: ThreadPool is in a state where it cannot submit tasks&#34;);
      }

      // 判断任务队列是否已满
      if (isTaskQueueFull())
      {
        VELOX_ERROR(&#34;[submit error]: task queue is full&#34;);
        throw std::runtime_error(&#34;[submit error]: task queue is full&#34;);
      }

      using return_type = std::invoke_result_t&lt;F, Args...&gt;;
      auto args_tuple = std::make_tuple(std::forward&lt;Args&gt;(args)...);
      // 将提交的任务进行双重包装:
      // 1. 内层 lambda 将 f 和其参数封装成一个无参可调用的对象. 统一了工作线程统执行任务的方式(通过()来执行)
      // 2. 外层 std::packaged_task 封装该 lambda. 这样做能够获取 future, 以便任务提交者能够获取执行结果
      auto task_ptr = std::make_shared&lt;std::packaged_task&lt;return_type()&gt;&gt;(
          [f = std::forward&lt;F&gt;(f), tuple = std::move(args_tuple)]() mutable -&gt; return_type { return std::apply(f, tuple); });

      // 获取该任务的 future, 通过该变量获取任务执行结果
      std::future&lt;return_type&gt; future = task_ptr-&gt;get_future();

      // 将任务提交到任务队列中
      {
        std::lock_guard&lt;std::mutex&gt; lock(m_task_queue_mutex);
        // 进行类型擦除, 将 packaged_task 封装为 void() 类型的 lambda 并加入任务队列
        // 使得任务队列能够存储不同返回类型的函数, 提高了通用性
        m_task_queue.emplace([task_ptr]() { (*task_ptr)(); });
      }

      m_task_queue_cv.notify_one();

      return future;
    }
```

代码中的注释已经详细的说明了每一行代码的作用，那么下面我们将详细说明一下这个函数的设计思路：

1. **如何设计任务队列？**
   首先，线程池的任务队列必须能够存储**各种各样**的任务，那么就意味着我们只能往里面塞 **`void()` 类型**的任务对象，换句话说，所有任务都必须 **“类型擦除”** 为一个 `void()` 可调用对象。因此，任务队列存储的类型就应该为`std::function&lt;void()&gt;`，这样就能够存储任何任务了，且工作线程也只管执行任务，无需关心任务的参数和返回值。
2. **如何将原始函数封装成`std::function&lt;void()&gt;` 呢？**
   任务提交者肯定都是想要知道任务执行结果的，而工作线程执行的是`void()`，有什么优雅的方式能够获取执行结果呢？那就是使用`std::packaged_task`来进行封装! 因为该类的`get_future()` 方法会返回一个 `std::future` 对象，可以获取任务的执行结果。

   所以，我们先使用一个 `lambda` 将原始任务封装成一个**无参任务**，再使用 `std::packaged_task` 对无参任务进行封装，最后在入队时将其封装成一个`void()`对象。

   这里有三个需要注意的地方：

   &lt;mark&gt;1. 为什么`std::packaged_task` 不直接将无参任务封装成`void()`?&lt;/mark&gt;

   因为当任务有返回值时，我们想要能够获取返回值。用 `packaged_task&lt;void()&gt;` 那么返回的 `future`类型就是`std::future&lt;void&gt;`，任务提交者就无法获取任务执行结果了。

   &lt;mark&gt;2. 为什么要用 `lambda` 将原始任务封装成一个无参任务再由`std::packaged_task`封装？&lt;/mark&gt;

   因为，这种做法是标准做法，其提供了更直接的参数绑定，可以保持任务签名一致性 (`void()`)，代码结构更加清晰健壮。当然也可以使用`std::packaged_task`封装原始函数，在后续再把task封装成`void()`，但是不推荐这样做。

   &lt;mark&gt;3. 为什么使用 `shared_ptr` 管理 `packaged_task`？&lt;/mark&gt;

   最主要的原因是 **`packaged_task` 对象必须在 `submit` 函数返回后继续存活，直到某个工作线程最终执行它。**

   如果使用局部变量 `auto task = ...`，这个 `task` 对象会在 `submit` 函数结束时被销毁，当工作线程调用 `task()` 时，它访问的是一个**已经被销毁的对象**。这是典型的**悬挂指针/引用**问题，会导致**未定义行为**，程序很可能会立即崩溃。

   此外，`std::function` 要求其内部存储的可调用对象是**可拷贝的**，而一个捕获了 `std::unique_ptr` 的 lambda 自身是不可拷贝的，所以不能使用`std::unique_ptr`。

**注意：** 如果提交的任务有引用参数的话，要使用 **`std::ref(data)`** 的形式来传，因为`Args&amp;&amp;... args` 是值传递，实参会被 按值 传递进任务中（即复制），除非你明确告诉它传“引用”。

### 工作线程执行逻辑

工作线程就是不断执行定义的循环逻辑，直到被线程池终止。

```cpp
  void ThreadPool::WorkerThread::run()
  {
    while (true)
    {
      // --- 阶段1: 检查自身状态 ---
      {
        // 状态互斥量上锁
        std::unique_lock status_lock(m_status_mutex);

        // 处理终止
        if (m_status.load(std::memory_order_acquire) == Status::TERMINATING)
        {
          m_status.store(Status::TERMINATED, std::memory_order_release);
          break;
        }

        // 处理暂停
        if (m_status.load(std::memory_order_acquire) == Status::PAUSED)
        {
          status_lock.unlock();   // 手动释放状态锁
          m_pause_sem.acquire();  // 等待信号量
          continue;
        }
      }

      // --- 阶段2: 处于运行态, 获取任务 ---
      std::function&lt;void()&gt; task;
      {
        // 任务队列上锁
        std::unique_lock queue_lock(m_pool_ptr-&gt;m_task_queue_mutex);

        // 判断是否 wait
        while (!isWake())
        {
          m_pool_ptr-&gt;m_task_queue_cv.wait(queue_lock);
        }

        /*--------------- 判断被唤醒的原因 ---------------*/
        // 1. 线程处于非运行态
        {
          std::shared_lock status_lock(m_status_mutex);
          if (m_status.load(std::memory_order_acquire) != Status::RUNNING)
          {
            continue;
          }
        }

        // 2. 线程池要停止了, 且队列已空, 则直接准备退出
        if (m_pool_ptr-&gt;m_terminating_flag.load(std::memory_order_acquire) &amp;&amp; m_pool_ptr-&gt;m_task_queue.empty())
        {
          std::unique_lock status_lock(m_status_mutex);
          m_status.store(Status::TERMINATING, std::memory_order_release);
          continue;
        }

        // 3. 线程处于运行态, 对于 终止 &#43; 非空 和 非终止 &#43; 非空 的情况我们都需要取任务执行
        task = std::move(m_pool_ptr-&gt;m_task_queue.front());
        m_pool_ptr-&gt;m_task_queue.pop();

        if (m_pool_ptr-&gt;m_task_queue.empty())
        {
          m_pool_ptr-&gt;m_task_queue_empty_cv.notify_all();
        }
      }

      // --- 阶段3: 执行任务(无锁状态) ---
      m_pool_ptr-&gt;m_busy_thread_count.fetch_add(1, std::memory_order_release);

      try
      {
        if (task)
        {
          task();  // 执行任务
        }
      }
      catch (const std::exception&amp; e)
      {
        VELOX_ERROR(&#34;Task execution failed: {}&#34;, e.what());
      }

      m_pool_ptr-&gt;m_busy_thread_count.fetch_sub(1, std::memory_order_release);
      m_last_active_time.store(std::chrono::steady_clock::now(), std::memory_order_release);
    }

    VELOX_INFO(&#34;WorkerThread terminated&#34;);
  }
```

## 使用示例

```cpp
#include &lt;vector&gt;
#include &lt;chrono&gt;
#include &lt;iostream&gt;
#include &#34;threadpool.hpp&#34;

int add(int a, int b)
{
    return a &#43; b;
}

int main()
{
    velox::threadpool::ThreadPoolConfig pool_config; // 定义线程池配置变量
    // 指定各项配置的值, 不指定将会使用默认值, 各项默认值可查阅 config/threadpool.yml 文件
    pool_config.max_task_count = 1000;
    pool_config.core_thread_count = 6;
    pool_config.max_thread_count = 12;

    // 定义线程池变量
    std::unique_ptr&lt;ThreadPool&gt; pool = std::make_unique&lt;ThreadPool&gt;(pool_config);

    // 加载指定目录下的配置文件(可选)
    // velox::config::Config::loadFromConfDir(&#34;test/threadpool&#34;);

    // 提交一个返回 int 的普通函数
    auto future1 = pool-&gt;submit(add, 10, 20);

    // 提交一个无返回值的 Lambda 表达式
    auto future2 = pool-&gt;submit([] {
        std::this_thread::sleep_for(std::chrono::milliseconds(500));
        std::cout &lt;&lt; &#34;Task [lambda]: Finished.&#34; &lt;&lt; std::endl;
    });

    // 提交多个任务
    std::vector&lt;std::future&lt;int&gt;&gt; futures;
    for (int i = 0; i &lt; 5; &#43;&#43;i) 
    {
        futures.emplace_back(pool-&gt;submit([i] {
            std::cout &lt;&lt; &#34;Task [loop &#34; &lt;&lt; i &lt;&lt; &#34;]: Running in thread &#34; &lt;&lt; std::this_thread::get_id() &lt;&lt; std::endl;
            std::this_thread::sleep_for(std::chrono::milliseconds(200));
            return i * i;
        }));
    }

    // 暂停线程池, 线程池处于暂停态时只会接受任务, 不会执行任务
    pool-&gt;pause();

    // 在暂停期间提交一个任务，它会被接收但不会立即执行
    auto future_paused = pool-&gt;submit([] {
        std::cout &lt;&lt; &#34;This task was submitted while paused.&#34; &lt;&lt; std::endl;
    });
    std::this_thread::sleep_for(std::chrono::seconds(2)); // 等待一段时间

    // 将线程池恢复运行态
    pool-&gt;resume();

    // 获取任务结果
    std::cout &lt;&lt; &#34;Result of add(10, 20) is &#34; &lt;&lt; future1.get() &lt;&lt; std::endl;

    // 等待 lambda 任务完成 
    future2.get();

    // 等待循环中的任务并打印结果
    for (size_t i = 0; i &lt; futures.size(); &#43;&#43;i) 
    {
        std::cout &lt;&lt; &#34;Result of loop task &#34; &lt;&lt; i &lt;&lt; &#34; is &#34; &lt;&lt; futures[i].get() &lt;&lt; std::endl;
    }
    
    future_paused.get();
    
    std::cout &lt;&lt; &#34;\nAll tasks finished.&#34; &lt;&lt; std::endl;
    
    // 关闭线程池, 但也可以不调用此函数, 由线程池类的析构函数完成资源的释放
    pool-&gt;shutdown();
    return 0;
}
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/e87181a/  

