条件变量（std::condition_variable）是 C++ 中的一个同步原语，它允许多个线程等待某个条件成立。它通常与互斥锁（std::mutex）一起使用，以实现线程间的同步。
条件变量提供了两个主要操作：wait 和 notify。wait 操作用于阻塞当前线程，直到收到通知或满足指定的条件。notify 操作用于唤醒一个或多个等待的线程。
以下是 condition_variable 的一些主要用途：
线程同步：条件变量可以用于同步多个线程的操作。例如，你可能有一个线程在等待另一个线程完成某项任务。在这种情况下，你可以使用条件变量来使等待的线程挂起，直到任务完成。
生产者-消费者问题：条件变量可以用于解决经典的生产者-消费者问题。在这个问题中，你可能有一个生产者线程和一个消费者线程。生产者线程生成数据，消费者线程消费数据。如果数据尚未准备好，消费者线程应该等待。当数据准备好时，生产者线程可以使用条件变量来通知消费者线程2。
防止虚假唤醒：虚假唤醒是指一个线程在没有收到通知的情况下被唤醒。使用条件变量可以防止这种情况的发生1。
假设我们有一个生产者线程和一个消费者线程，它们共享一个队列。生产者线程不断地向队列中添加元素，消费者线程不断地从队列中取出元素。当队列为空时，消费者线程需要等待生产者线程添加新
示例代码：

#include <iostream>
#include <queue>
#include <thread>
#include <mutex>
#include <condition_variable>

std::queue<int> queue; // 共享队列
std::mutex mutex; // 保护队列的互斥锁
std::condition_variable condition; // 条件变量

void producer() {
    for (int i = 0; i < 10; ++i) {
        std::unique_lock<std::mutex> lock(mutex); // 获取互斥锁
        queue.push(i); // 向队列中添加元素
        lock.unlock(); // 释放互斥锁
        condition.notify_one(); // 唤醒一个等待的消费者线程
    }
}

void consumer() {
    while (true) {
        std::unique_lock<std::mutex> lock(mutex); // 获取互斥锁
        condition.wait(lock, [] { return !queue.empty(); }); // 等待队列非空
        int value = queue.front(); // 获取队列中的第一个元素
        queue.pop(); // 从队列中移除第一个元素
        lock.unlock(); // 释放互斥锁
        std::cout << "Consumer got: " << value << std::endl; // 输出获取到的元素值
    }
}

int main() {
    std::thread t1(producer); // 创建生产者线程
    std::thread t2(consumer); // 创建消费者线程

    t1.join(); // 等待生产者线程结束
    t2.join(); // 等待消费者线程结束
    
    return 0;
}

在这个例子中，我们创建了两个线程：生产者线程和消费者线程。生产者线程不断地向共享队列中添加元素，并使用条件变量的 notify_one 操作唤醒一个等待的消费者线程。消费者线程则使用条件变量的 wait 操作等待队列非空，并从队列中取出元素进行处理。

总之，条件变量是 C++ 中用于实现多线程同步的一种机制。它允许多个线程等待某个条件成立，并在条件成立时被唤醒继续执行。它通常与互斥锁一起使用，以保护共享数据并避免竞态条件。。

互斥锁：
互斥锁（Mutex）是一种用于保护共享资源的同步原语。它可以确保在任何时刻只有一个线程能够访问共享资源。当一个线程想要访问共享资源时，它必须先锁定互斥锁。如果互斥锁已经被其他线程锁定，那么该线程将被阻塞，直到互斥锁被解锁。当线程完成对共享资源的访问后，它必须解锁互斥锁，以便其他线程可以访问共享资源。

C++死锁是指两个或多个线程在执行过程中，因为争夺资源或者通信而造成的一种相互等待的现象，如果没有外部干预，它们都无法继续执行。C++死锁通常发生在多线程编程中，当多个线程需要同时获取多个互斥锁时，就有可能出现死锁。

右值（即一个临时的、不可寻址的、可以提供数据值的表达式），从而实现移动语义和完美转发等功能

worker.join() 是一个成员函数，它属于 std::thread 类。这个函数会阻塞调用它的线程，直到与之关联的线程执行完成。这样可以同步调用 join() 函数的线程和与之关联的线程的执行时刻。
它允许您定义一个匿名函数对象，也称为闭包。Lambda表达式通常用于定义简短的、一次性的函数，它们可以在函数内部定义并使用。

std::placeholders::_1 是一个占位符，它用于 std::bind 函数中，表示绑定的函数的第一个参数。当您使用 std::bind 函数将一个函数与其参数绑定在一起时，您可以使用占位符来表示绑定函数的参数。


C++14:
增强了 std::shared_ptr 的功能。
C++17:
std::pmr 命名空间：它提供了一种新的内存分配抽象，可以让程序员更容易地实现和使用自定义的内存资源，包括内存池。
std::optional：提供了一种可选值的概念，可以用来表示一个可能不存在的值。这个类模板可以帮助程序员避免使用指针和动态分配来表示可选值。
C++20:
std::coroutine：引入了协程（coroutine）支持，它提供了一种新的编程范式，可以帮助程序员更容易地编写异步代码。协程可以暂停执行并在稍后恢复，这样可以避免阻塞线程并提高程序的响应能力。
std::span：C++20 引入了 std::span 类模板，它提供了一种轻量级的视图，可以用来表示一个数组或其他连续序列的一部分。这个类模板可以帮助程序员避免使用指针和长度参数来传递数组。



std::unique_ptr：这是一种独占所有权的智能指针，它管理一个动态分配的对象，并在 std::unique_ptr 对象销毁时自动删除该对象。std::unique_ptr 不能被复制，但可以被移动，这意味着您可以将它的所有权从一个 std::unique_ptr 对象转移到另一个 std::unique_ptr 对象。

std::shared_ptr：这是一种共享所有权的智能指针，它管理一个动态分配的对象，并在最后一个 std::shared_ptr 对象销毁时自动删除该对象。std::shared_ptr 可以被复制和移动，每个 std::shared_ptr 对象都共享对同一个对象的所有权。

std::weak_ptr：这是一种弱引用智能指针，它指向一个由 std::shared_ptr 对象管理的对象，但不影响该对象的引用计数。std::weak_ptr 主要用于解决循环引用问题。


内存池（Memory Pool）是一种动态内存分配与管理技术。通常情况下，程序员习惯直接使用 new，delete，malloc，free 等 API 申请和释放内存。这样导致的后果就是：当程序运行的时间很长的时候，由于所申请的内存块的大小不定，频繁使用时会造成大量的内存碎片从而降低程序和操作系统的性能1。

内存池则是在真正使用内存之前，先申请分配一大块内存（内存池）留作备用。当程序员申请内存时，从池中取出一块动态分配，当程序员释放时，将释放的内存放回到池内，再次申请，就可以从池里取出来使用，并尽量与周边的空闲内存块合并。若内存池不够时，则自动扩大内存池，从操作系统中申请更大的内存池1。

在 C++17 中，并没有直接提供内存池技术的实现。然而，C++17 提供了一些新特性和工具，如 std::pmr::memory_resource 和 std::pmr::polymorphic_allocator，这些可以用来实现自定义的内存池2。

std::mutex：互斥锁，提供了最基本的锁定和解锁操作。它不能被同一个线程多次锁定，否则会导致死锁。
std::recursive_mutex：递归互斥锁，与 std::mutex 类似，但允许同一个线程多次锁定。每次锁定都必须对应一次解锁。
std::timed_mutex：定时互斥锁，与 std::mutex 类似，但提供了额外的定时锁定功能。它允许您尝试在指定时间内锁定互斥锁，如果超时则返回失败。
std::recursive_timed_mutex：递归定时互斥锁，结合了 std::recursive_mutex 和 std::timed_mutex 的特点。它允许同一个线程多次锁定，并提供了定时锁定功能。
std::shared_mutex（C++17）：共享互斥锁，允许多个线程同时读取共享数据，但只允许一个线程写入共享数据。它提供了两种类型的锁定操作：共享锁定和独占锁定。
std::shared_timed_mutex（C++14）：共享定时互斥锁，与 std::shared_mutex 类似，但提供了额外的定时锁定功能。它允许您尝试在指定时间内进行共享或独占锁定。


C++ 并发计算指的是在 C++ 程序中使用多线程或多进程来同时执行多个计算任务。C++ 提供了多种并发编程工具，包括线程、互斥锁、条件变量、原子操作和异步任务等，可以帮助程序员更容易地编写并发程序。

当你在一个线程对象上调用 join() 函数时，当前线程（即调用 join() 的线程）将被阻塞，直到被 join() 的线程执行完毕。

join() 函数的主要作用是确保线程按顺序执行。例如，如果你有两个线程 t1 和 t2，并且希望在 t1 完成执行后再执行 t2，那么你可以在启动 t2 之前对 t1 调用 join() 函数

引用通常用于函数参数和返回值。当你将一个变量作为引用类型的参数传递给函数时，函数将获得对该变量的直接访问权限。这意味着函数可以修改该变量的值，而不仅仅是它的副本。
void swap(int& a, int& b) {
    int temp = a;
    a = b;
    b = temp;
}

int main() {
    int x = 5;
    int y = 10;
    swap(x, y);
    std::cout << "x: " << x << ", y: " << y << std::endl;
    return 0;
}


C++20线程特性
std::jthread：这是一种新的线程类，它提供了对 std::stop_token 的支持，允许线程在退出时更好地协调。与 std::thread 不同，std::jthread 在析构时会自动加入或请求停止。
std::stop_token 和 std::stop_source：这些类提供了一种协调线程停止的机制。std::stop_source 可以用来发出停止请求，而 std::stop_token 可以用来接收停止请求。
半期和关联的等待函数：C++20 引入了 std::latch 和 std::barrier 两种新的同步原语，以及一组与之关联的等待函数，如 std::wait 和 std::notify_all_at_thread_exit。这些新特性提供了更多的控制和灵活性，以协调多个线程之间的操作。
原子智能指针：C++20 引入了对原子操作智能指针（如 std::atomic<std::shared_ptr>）的支持。这允许多个线程安全地访问和修改共享对象，而无需使用互斥锁或其他同步原语。


forward函数用于实现完美转发。它能够将函数的参数原封不动地传递到下一个函数中。这个“原封不动”指的是，如果输入的参数是左值，那么传递给下一个函数的参数也是左值；如果输入的参数是右值，那么传递给下一个函数的参数也是右值。


template <typename T>
是C++中定义模板的固定格式。template是一个关键字，用于声明模板。typename是一个关键字，用于指定模板参数的类型。T是一个模板参数，它表示一个占位符，可以在实例化模板时被替换为实际的类型
模板是C++中一种非常强大的工具，它允许您编写通用的代码，可以用于不同的数据类型。例如，您可以编写一个通用的排序函数，它可以用于对不同类型的数组进行排序。这样，您就不需要为每种数据类型都编写一个单独的排序函数。
下面是一个简单的例子，演示了如何使用模板定义一个通用的最大值函数：
#include <iostream>
template <typename T>
T myMax(T x, T y) {
    return (x > y) ? x : y;
}

int main() {
    std::cout << myMax<int>(3, 7) << std::endl;
    std::cout << myMax<double>(3.0, 7.0) << std::endl;
    std::cout << myMax<char>('g', 'e') << std::endl;
    return 0;
}

unique_ptr实例
#include<iostream>
#include<memory>

using namespace std;

struct Song
{
	string artist;
	string title;
	Song(const string& artist, const string& title)
		: artist(artist), title(title) {}
};

int main() {
	unique_ptr<Song> song1 = make_unique<Song>("Mr xi", "My dream");
	cout << "Artist:" << song1->artist << endl;
	cout << "Title:" << song1->title << endl;
	unique_ptr<Song> song2 = move(song1);
	if (song1)
		cout << "song1 not null" << endl;
	else
		cout << "song1 is null" << endl;
	if (song2)
		cout << "song2 not null" << endl;
	else
		cout << "song2 is null" << endl;
	return 0;
}


shared_ptr实例
#include <iostream>
#include <memory>
using namespace std;

struct book {
	string title;
	string author;
	book(string title, string author)
		:title(title), author(author){};
};

int main()
{
	shared_ptr<book> book1 = make_shared<book>("My_Struggle","Mr_Xi");
	cout << "book1 title:" << book1->title << endl;
	cout << "book1 author:" << book1->author << endl;
	shared_ptr<book> book2 = book1;
	shared_ptr<book> book3 = book2;
	cout << "count: " << book1.use_count() << endl;
	return 0;
}


weak_ptr实例
#include <iostream>
#include <memory>

struct Foo {
    Foo() { std::cout << "Foo constructed\n"; }
    ~Foo() { std::cout << "Foo destroyed\n"; }
};

int main() {
    std::shared_ptr<Foo> sptr = std::make_shared<Foo>();
    std::weak_ptr<Foo> wptr = sptr;

    // 销毁shared_ptr
    sptr.reset();
    
    // 检查weak_ptr是否过期
    if (wptr.expired()) {
        std::cout << "Weak pointer expired\n";
    }
    else {
        std::cout << "Weak pointer still valid\n";
    }
    
    return 0;
}

互斥锁实例
#include <iostream>
#include <thread>
#include <mutex>

using namespace std;
mutex mtx; // 创建互斥锁
int counter = 0; // 共享资源

void increase10k() {
    for (int i = 0; i < 10000; ++i) {
        mtx.lock();
        ++counter;
        mtx.unlock();
    }
}

int main() {
    thread threads[10]; //只是个数组
    for (int i = 0; i < 10; ++i) {
        threads[i] = thread(increase10k); // 创建10个线程
    }
    for (auto &th : threads) {
        th.join(); // 等待所有线程结束
    }
    cout << "Counter: " << counter << endl;
    return 0;
}

条件变量实例：
#include <iostream>
#include <queue>
#include <thread>
#include <mutex>
#include <condition_variable>

std::queue<int> produced_nums;  // 生产的数字队列
std::mutex m;  // 互斥锁
std::condition_variable cond_var;  // 条件变量
bool done = false;  // 生产者是否完成生产的标志
bool notified = false;  // 是否已通知消费者的标志

void producer(int n)
{
    for (int i = 0; i < n; ++i) {
        std::unique_lock<std::mutex> lock(m);  // 锁住互斥锁
        produced_nums.push(i);  // 生产数字并添加到队列
        notified = true;  // 设置已通知标志为 true
        cond_var.notify_all();  // 唤醒所有等待的线程
    }

    done = true;  // 设置生产完成标志为 true
    cond_var.notify_all();  // 唤醒所有等待的线程
}

void consumer()
{
    while (!done) {  // 如果生产未完成，则继续消费
        std::unique_lock<std::mutex> lock(m);  // 锁住互斥锁
        while (!notified) {  // 如果未被通知，则等待通知
            cond_var.wait(lock);  // 等待条件变量的通知
        }
        while (!produced_nums.empty()) {  // 如果队列不为空，则继续消费
            std::cout << produced_nums.front() << ' ';  // 打印队首元素
            produced_nums.pop();  // 弹出队首元素
        }
        notified = false;  // 设置已通知标志为 false，等待下一次通知
    }
}

int main()
{
    std::thread p(producer, 10);  // 创建生产者线程，生产10个数字
    std::thread c(consumer);  // 创建消费者线程

    p.join();  // 等待生产者线程结束
    c.join();  // 等待消费者线程结束
    
    return 0;
}


内存池是一种动态内存分配与管理技术，它可以提高内存使用效率并减少内存碎片。在 C++ 中，你可以使用一些基本的数据结构和算法来实现一个简单的内存池。

内存池示例，它使用了一个预先分配的内存块来存储空闲内存块，并使用链表来维护这些空闲块。当有新的内存需求时，就从内存池中分出一部分内存块，若内存块不够再继续申请新的内存，当内存释放后就回归到内存块留作后续的复用。

using namespace std;

class MemoryPool  // 定义内存池类
{
private:
    size_t blockSize;  // 内存块大小
    size_t blockCount;  // 内存块数量
    vector<char> data;  // 预分配的内存
    vector<void*> freeBlocks;  // 空闲块列表

public:
    MemoryPool(size_t blockSize, size_t blockCount)
        : blockSize(blockSize), blockCount(blockCount)  // 初始化内存块大小和数量
    {
        data.resize(blockSize * blockCount);  // 预分配内存
        //data是上面的成员变量，resize是vector的成员函数用于调整容器大小为括号里的值（大小乘数量）
        for (size_t i = 0; i < blockCount; ++i)  // 初始化空闲块列表
            freeBlocks.push_back(&data[blockSize * i]);  // 将预分配的内存添加到空闲块列表
            //push_back作用是把参数加到vector里
    }

    void* allocate()  // 分配内存
    {
        if (freeBlocks.empty())  // 如果没有空闲块，则返回 nullptr
            return nullptr;
        void* ptr = freeBlocks.back();  // 获取一个空闲块
        freeBlocks.pop_back();  // 从空闲块列表中移除该块
        return ptr;  // 返回该块的地址
    }
    
    void deallocate(void* ptr)  // 回收内存
    {
        freeBlocks.push_back(ptr);  // 将回收的内存添加到空闲块列表
    }
};
int main()  // 主函数
{
    MemoryPool pool(16, 10);  // 创建一个内存池，每个块大小为16字节，共10个块
    int* a = static_cast<int*> (pool.allocate());  // 分配一个整数大小的内存块
    *a = 10;  // 将该内存块的值设置为10
    cout << *a << endl;  // 输出该内存块的值
    pool.deallocate(a);  // 回收该内存块
    return 0;  // 返回0表示程序正常结束
}

这个示例中，MemoryPool 类使用了一个 std::vector<char> 来预先分配一大块内存，并使用另一个 std::vector<void*> 来维护空闲块。allocate 函数用于从内存池中分配一个块，如果没有空闲块，则返回 nullptr。deallocate 函数用于将一个块释放回内存池。