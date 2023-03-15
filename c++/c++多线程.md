# C++多线程

## 基本语法

头文件

```cpp
#include <thread> //win
#include <pthread>    //linux
```

创建与释放

```cpp
thread t1(函数指针,任意参数...);
t1.join();
```

函数指针可以用全局函数，后面的参数是全局函数的参数。

线程id

```cpp
this_thread::get_id();
```

线程睡眠

```cpp
this_thread::sleep_for(chrono::seconds(1));//睡眠一秒
```

## 线程的生命周期

构造时传入的函数指针就是线程的入口，构造完成就创建了线程。

thread对象销毁，线程即销毁。

如果想让线程继续运行，可用

```cpp
th.detach();
```

分离子线程和主线程，创建守护线程(后台运行的线程)。

问题：主线程退出后，分离的子线程不会同时退出，子线程如果又访问程序空间(程序空间已经被释放掉了)，会出错。

如果使用了detach，不要再访问外部变量，或者主线程退出前，利用全局变量通知子线程退出。

如果不使用detach，就要维护thread对象。

## 创建线程的传参问题

如果用值传递，会拷贝一份，没问题，以类作为参数为例，要经历两次拷贝构造。

如果用指针或引用传递，优点是省略了拷贝，节省空间。如果使用detach()，则当主线程崩溃或者正常结束后，该块内存被回收，若此时子线程没有结束，那么子线程中指针就成了野指针，程序会出错。

注：引用一定是std:ref显示指明，否则都是调用拷贝构造函数。

如何解决？

确保对象生命周期与线程生命周期一致。

## 用成员函数作为线程入口

```cpp
thread th(&MyThread::MainThread, &myth);
```

## 多线程中只初始化一次

```cpp
void SystemInitOnce(){
    static std::once_flag flag;
    std::call_once(flag,SystemInitOnce);
}
```

## 多线程状态的概念

初始化：线程正在被创建。

就绪：该线程在就序列表中，等待CPU调度。

运行：线程正在运行。

阻塞：线程正在被阻塞挂起，包括pend(锁、事件、信号量)，suspend(主动)，delay(延时阻塞)，pendtime(超时等待)。

退出：线程运行结束，等待父线程回收资源。

## 锁

### 竞争状态与临界区

竞争状态：多线程同时读写数据。

临界区：读写共享数据的代码。

### 锁的基本语法

```cpp
static mutex mux;
mux.lock();//获取锁，没有则等待
mux.unlock();//释放锁，不释放则死锁
mux.try_lock();//尝试获取锁，返回true表示成功，一般配合if语句
if(mux.try_lock()){
    this_thread::sleep_for(100ms);//每隔100ms尝试获取一次锁
}
```

### 互斥锁

可能的问题：unlock后立即调用lock，同一个线程一直占据资源。

解决方法：在unlock后阻塞一小段时间，让操作系统完全释放资源。

### 超时锁

作用：避免长时间死锁。

如果在一段时间内没有获得锁，进入超时状态。

```cpp
timed_mutex tmux;
tmux.try_lock_for(chrono::milliseconds(1000)); //try_lock+sleep
```

### 递归锁(可重入锁)

同一个锁可以被锁多次。

调用lock，锁计数加一，调用unlock，锁计数减一。

```cpp
recursive_mutex rmux;
```

### 共享锁

写互斥而读不互斥。

如果一个线程在写，其它线程都不能读写；

如果一个线程在读，其它线程都可以读，都不能写。

```cpp
shared_timed_mutex stmux; //c++14 共享超时互斥锁
shared_mutex smux;    //c++17    共享互斥锁
stmux.lock_shared();  //共享
stmux.unlock_shared();  
stmux.lock();    //排他
stmux.unlock();
```

自旋锁：自旋锁（spin lock）属于busy-waiting类型锁。在多处理器环境中，自旋锁最多只能被一个可执行线程持有。如果一个可执行线程试图获得一个被其它线程持有的自旋锁，那么线程就会一直进行忙等待，自旋（空转），等待自旋锁重新可用。如果自旋锁未被争用，请求锁的执行线程便立刻得到自旋锁，继续执行。
自旋锁一直处于用户态，非自旋锁会进入内核态。

自旋锁占用CPU资源不释放，执行直接过长会降低系统性能，因此引入了超时自旋锁。

## RAII

资源获取即初始化。

使用局部对象来管理资源，由操作系统管理其生命周期。

```cpp
class XMutex{
public:
    Xmutex(mutex &mux):mux(mux_){
        mux.lock();
    }
    ~XMutex(){
        mux.unlock();
    }
private:
    mutex &mux_;
};
static mutex mux;
void TestMutex(){
    XMutex lock(mux);
    //不用再管理mux的释放，析构时会自动释放。
    //析构时机即为函数结束。
}
```

### 自己实现的线程基类

```cpp
#pragma once
#include <thread>
class XThread {
public:
    virtual void Start() {
        th_ = std::thread(&XThread::MainThread, this);
    }
    virtual void Wait() {
        if (th_.joinable())
            th_.join();
    }
    virtual void Stop() {
        is_exit_ = true;
        Wait();
    }
    bool is_exit() {
        return is_exit_;
    }
private:
    virtual void MainThread() = 0;//线程入口函数接口
    std::thread th_;
    bool is_exit_ = false;
};
```

## c++11支持的RAII资源互斥管理

### c++11 lock_gard

```cpp
static mutex gmutex;
lock_gard<mutex> lock(gmutex);//没有默认构造
//作用域是{}内部
//如果已经锁住了，例如
gmutex.lock();
lock_gard<mutex> lock(gmutex);//会抛出异常
lock_gard<mutex> lock(gmutex,std::adopt_lock);//不会异常
//adopt_lock指定锁定策略，表示获取当前锁的所有权(已经拥有锁，不再加锁，出栈释放)
```

缺点：锁定区域较大，不够灵活。不能复制，不能移动。

### c++11 unique_lock

unique_lock提供了lock()和unlock()接口，用于临时释放锁；析构时，根据锁的状态决定是否释放锁。

不能复制，可以移动。

```cpp
//支持adopt_lock,defer_lock,try_to_lock
//defer_lock 延后加锁，不拥有锁，出栈不释放
//try_to_lock 尝试获取锁的所有权，不阻塞，出栈不释放
static mutex mux;
{
    unique_lock<mutex> lock(mux);
    lock.unlock();//临时释放锁
    lock.lock();//重新加锁
}
{
    mux.lock();
    unique_lock<mutex> lock(mux,adopt_lock);
}
{
    unique_lock<mutex> lock(mux,defer_lock);//临时加锁
    lock.lock();
}

{
    unique_lock<mutex> lock(mux,try_to_lock);//尝试加锁
    if(lock.owns_lock()){
    //判断是否拥有锁
    }
}
```

### c++14 shared_lock

```cpp
static shared_timed_mutex mux;
{
    shared_lock<shared_timed_mutex> lock(mux);
    //以共享方式锁定，其他线程可以获得
    //读取
}
{
    unique_lock<shared_timed_mutex> lock(mux);
    //以独占方式锁定
    //写入
}
```

### c++17 scoped_lock

用于多个锁的封装器。

```cpp
//示例
static mutex mux1;
static mutex mux2;
void test01(){//线程1入口
    this_thread::sleep_for(100ms);
    mux1.lock();
    mux2.lock();
    //业务代码
    this_thread::sleep_for(1000ms);
    mux1.unlock();
    mux2.unlock();
}
void test02(){//线程2入口
    mux2.lock();
    this_thread::sleep_for(100ms);//模拟死锁，等另一个线程锁mux2
    mux1.lock();
    //业务代码
    this_thread::sleep_for(1000ms);
    mux1.unlock();
    mux2.unlock();
}
//死锁，线程1锁住mux1，线程2锁住mux2
//如何解决？
lock(mux1,mux2);//c++11    要么同时锁，要么都不锁(哲学家进餐)
scope_lock lock(mux1,mux2);//c++17    
```

### 利用互斥锁实现线程通信

```cpp
#include <iostream>
#include <list>
#include <mutex>
#include <string>
#include <chrono>
#include"ThreadBase.h"

//#include <pthread>    //linux
class MsgServer : public XThread {
public:
    void SendMsg(std::string msg) {
        std::unique_lock<std::mutex> lock(mux_);
        if (msgs_.size() >= maxlen)
            return;
        msgs_.push_back(msg);
    }
    MsgServer():maxlen(10) {}
private:
    void MainThread() override{//处理消息队列
        while (!is_exit()) {
            std::this_thread::sleep_for(std::chrono::milliseconds(10));
            std::unique_lock<std::mutex> lock(mux_);
            if (msgs_.empty())
                continue;
            while (!msgs_.empty()) {
                std::cout << "recv: " << msgs_.front() << std::endl;
                msgs_.pop_front();
            }
        }
    }
    std::list<std::string> msgs_;
    const int maxlen;
    std::mutex mux_;
};
```

```cpp

```

## 条件变量

锁同步机制的问题：消费者不停等待，占用cpu资源。

如何改进：让消费者阻塞。

生产者线程：

```cpp
std::condition_variable cv;//信号量
unique_lock lock(mux);//获得mutex
msgs_.push_back(data);//获取锁时修改
lock.unlock();//释放锁
cv.notify();//通知等待线程，解除阻塞
```

消费者线程：

```cpp
unique_lock lock(mux);//获得锁
cv.wait(lock);//先解锁，等待通知
//接到通知自动上锁
//处理数据……
//wait中可以加lambda表达式
```

## 线程异步和通信

promise和future可以在不同线程间传递信息。

```cpp
std::promise
//提供存储异步通信的值，只能使用一次
std::future
//异步获得结果
//用例
void TestFuture(){
    p.set_value("TestFuture value");
}
{
    promise<string> p;//异步传输变量
    auto future = p.get_future();//获取线程写入值
    auto th = thread(TestFuture,move(p));
    cout << future.get() << endl;//最终会打印set_value中的内容
    th.join();
}
```

packaged_task包装函数为一个对象，用于异步调用。函数调用和返回值获取被拆分为两步。

```cpp
//头文件
#include <future>
//# ...
string TestPack(int index){
    cout << index << endl;
    this_thread::sleep_for(1s);
    return "TestPack return";
}
{
    package_task<string(int)> task(TestPack);//task是函数指针
    auto result = task.get_future();
    thread th(move(task),100);
    //测试是否超时
    for(int i=0;i<10;i++){
        if(result.wait_for(100ms) != future_status::ready)
            continue;

    }
    if(result.wait_for(100ms) == future_status::timeout){
        //超时处理
    }
    cout << result.get();//阻塞等待返回值，获取结果
    th.join();
}
```

async异步调用函数，并返回结果。

```cpp
using namespace std;
string TestAsync(int index)
{
    cout << "begin in TestAsync" << this_thread::get_id() << endl;
    this_thread::sleep_for(2s);
    return "TestAsync return";
}

{//不创建线程启动异步操作
    cout << "main thread" << this_thread.get_id() << endl;
    auto future = async(launch::deferred,TestAsync,100);
    cout << "get result" << future.get() << endl;//get阻塞等待异步任务结束
}
{//创建线程启动异步操作
    auto future = async(TestAsync,100);
    cout << "get result" << future.get() << endl;
}
```

launch::deferred参数表示延迟执行，在调用wait或get时才进入函数。

launch::async参数表示创建线程去执行异步操作。
