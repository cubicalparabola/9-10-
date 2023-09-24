shared_ptr
这是一种共享所有权的智能指针，它管理一个动态分配的对象，并在最后一个shared_ptr 对象销毁时自动删除该对象。shared_ptr 可以被复制和移动，每个shared_ptr 对象都共享对同一个对象的所有权。

构造函数：用于创建一个新的shared_ptr实例。
析构函数：当shared_ptr实例被销毁时，析构函数会被调用，它会减少对所指向对象的引用计数。如果引用计数变为0，那么这个对象就会被自动删除。
operator->：这个运算符被重载，使得我们可以像使用普通指针一样使用shared_ptr。
operator*：这个运算符也被重载，它返回一个指向所管理对象的引用。
reset()：这个方法可以更改shared_ptr所指向的对象，或者使其不再指向任何对象。

下面是一个实例
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
  
