# c++11多线程

详见https://www.apiref.com/cpp-zh/cpp/thread/thread.html
## 如何创建线程
```
#include<thread>
//线程类std::thread
//构造函数
thread() noexcept;
thread( thread&& other ) noexcept;//移动构造，移动后，原线程对象不再代表线程。
template< class Function, class... Args > explicit thread( Function&& f, Args&&... args );//函数/仿函数/lambda对象，前者的参数
thread(const thread&) = delete;//线程不允许拷贝，删除了拷贝构造

/*
第三个最常用，只有它构造新的 std::thread 对象并将它与执行线程关联。新的执行线程开始执行
*/
```

## 线程的释放
1.线程的任务函数返回后，线程终止。
2.主线程退出，子线程全部终止。

两种回收方式
1.在主程序中调用join()等待子线程退出，回收它的资源。如果子线程运行结束，join()立即返回，否则阻塞直到子线程运行结束。
2.在主程序调用detach()分离子线程，子线程退出后自动回收资源；分离后不可join()。可以用joinable()判断是否分离。

## this_thread全局函数
```
std::thread::id get_id() const noexcept;//获取线程id，返回值为thread::id类型
//使用:
this_thread::get_id();

template< class Rep, class Period >
void sleep_for( const std::chrono::duration<Rep, Period>& sleep_duration );
//使用:
this_thread::sleep_for(chrono::seconds(1));//休眠1秒,seconds可换为minutes等

template< class Clock, class Duration >
void sleep_until( const std::chrono::time_point<Clock,Duration>& sleep_time );

void yield() noexcept;//让线程主动让出已抢占的时间片
```
下面是一段demo
```

```
