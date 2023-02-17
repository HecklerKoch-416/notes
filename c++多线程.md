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
#include<thread>
#include"global.h"
#include<Windows.h>
using namespace std;
//线程传参问题
//1.值传递
//2.引用传递
//线程的创建属于函数式编程，即使是用引用来接收传的值，也是会将其拷贝一份到子线程的独立内存中。
//因此&符号不能达到目的
//c++11引入了std::ref(); 接受ref的那个值，函数声明不能加const
//3.指针传递
//注意陷阱，不同线程用指针传递共享一个存储空间，先结束的线程会释放空间，后结束的可能访问野指针
void func(const string& s,int a) {
	this_thread::sleep_for(chrono::nanoseconds(1000000000));
	for (int i = 0; i < a; i++) {
		cout << s << "第" << i << "次" << endl;
		this_thread::sleep_for(chrono::seconds(1));//休眠1s
	}
}

void thread_main() {
	thread t1(func, "线程1", 10);
	Sleep(500);
	thread t2(func, "线程2", 10);
	//获取线程id
	Sleep(200);
	thread::id id_main= this_thread::get_id();
	cout << "主线程:" << id_main << endl;
	cout << "线程1:" << t1.get_id() << endl;
	cout << "线程2:" << t2.get_id() << endl;
	Sleep(1000);
	//交换线程
	t1.swap(t2);
	cout << "交换后" << endl;
	cout << "线程1:" << t1.get_id() << endl;
	cout << "线程2:" << t2.get_id() << endl;
	//转移构造
	thread t3 = move(t1);
	thread t4 = move(t2);
	cout << "转移后" << endl;
	cout << "线程1:" << t3.get_id() << endl;
	cout << "线程2:" << t4.get_id() << endl;
	//转移资源后,t1,t2不再代表线程，不能join
	//t1.join();
	//t2.join();
	t3.join();
	t4.join();
}
```
