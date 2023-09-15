[`std::lock_guard`是C++11的特性，它是一个互斥体包装器，为在作用域块期间占有互斥提供便利的RAII风格机制。当创建`std::lock_guard`对象时，它会试图接收给定互斥的所有权。当控制离开创建`std::lock_guard`对象的作用域时，`std::lock_guard`会被销毁并释放互斥。

在构造函数中，`std::lock_guard`会自动绑定它的互斥体并加锁，在析构函数中解锁。这种设计大大减少了死锁的风险。下面是一个基本的使用示例：

```cpp
#include <iostream>
#include <mutex>
#include <thread>

class Widget{
public:
    Widget() = default;
    ~Widget() = default;
    void fun() {
        std::lock_guard<std::mutex> lock(lock_);
        std::cout << "Widget::fun run" << std::endl;
    }
private:
    std::mutex lock_;
};

void TestThread1(Widget* w) {
    w->fun();
}

int main() {
    Widget* w = new Widget();
    std::thread t1(&TestThread1, w);
    t1.join();
    return 0;
}
```

在这个例子中，程序在`std::lock_guard`生命周期内加锁和解锁，其中加锁和解锁分别在构造函数和析构函数中完成。

std::lock_guard和std::unique_lock都是C++标准库中用于管理互斥量的类，它们之间的本质区别如下：

**所有权**：

std::lock_guard：拥有互斥量的自动锁定权。一旦创建std::lock_guard对象，它将对互斥量进行锁定，并在其作用域结束时自动释放锁定。
std::unique_lock：拥有互斥量的手动锁定权。可以在构造函数中选择是否立即锁定互斥量，并可以在其作用域内手动控制锁定和解锁的时机。

**灵活性**：

std::lock_guard：提供了一种简单的、固定的锁定机制，不支持手动解锁。在std::lock_guard对象的作用域内，互斥量将一直保持锁定状态，直到作用域结束。
std::unique_lock：提供了更大的灵活性。可以在构造函数中选择是否立即锁定互斥量，还可以手动调用lock()和unlock()函数来控制锁定和解锁的时机。

**条件变量的支持**：

std::lock_guard：不支持与条件变量一起使用。由于std::lock_guard无法手动解锁，因此无法满足条件变量等待和通知的需求。
std::unique_lock：支持与条件变量一起使用。通过手动调用unlock()和lock()函数，可以在适当的时机解锁和重新锁定互斥量，以与条件变量协同工作。

总的来说，std::lock_guard提供了一种简单的、固定的锁定机制，适用于大多数情况下简单的互斥访问。而std::unique_lock提供了更大的灵活性和更多的功能，例如手动控制锁定和解锁的时机，以及与条件变量的配合使用。因此，在需要更高级的互斥控制或与条件变量一起使用时，std::unique_lock是更适合的选择。
