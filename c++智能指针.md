# 智能指针
C 语言中最常使用的是malloc()函数分配内存，free()函数释放内存，而 C++ 中对应的是new、delete关键字。malloc()只是分配了内存，而new则更进一步，不仅分配了内存，还调用了构造函数进行初始化。
```
int main()
{
    // malloc返回值是 void*
    int* argC = (int*)malloc(sizeof(int));
    free(argC);

    char *age = new int(25); // 做了两件事情 1.分配内存 2.初始化
    delete age;
}

```

所以 C++11 中引入了智能指针（Smart Pointer），它利用了一种叫做 RAII（资源获取即初始化）的技术将普通的指针封装为一个栈对象。当栈对象的生存周期结束后，会在析构函数中释放掉申请的内存，从而防止内存泄漏。这使得智能指针实质是一个对象，行为表现的却像一个指针。

智能指针主要分为shared_ptr、unique_ptr和weak_ptr三种，使用时需要引用头文件<memory>。C++98 中还有auto_ptr，基本被淘汰了，不推荐使用。而 C++11 中shared_ptr和weak_ptr都是参考boost库实现的。
  
## shared_ptr共享的智能指针
最安全的分配和使用动态内存的方法是调用一个名为 make_shared 的标准库函数。 此函数在动态内存中分配一个对象并初始化它，返回指向此对象的 shared_ptr。与智能指针一样，make_shared 也定义在头文件 memory 中。
  例：
  ```
  // 指向一个值为42的int的shared_ptr
  shared_ptr<int> p3 = make_shared<int>(42);

  // p4 指向一个值为"9999999999"的string
  shared_ptr<string> p4 = make_shared<string>(10,'9');

  // p5指向一个值初始化的int
  shared_ptr<int> p5 = make_shared<int>();
  ```
我们还可以用 new 返回的指针来初始化智能指针，不过接受指针参数的智能指针构造函数是 explicit 的。因此，我们不能将一个内置指针隐式转换为一个智能指针，必须使用直接初始化形式来初始化一个智能指针：
  ```
  shared_ptr<int> pi = new int (1024); // 错误：必须使用直接初始化形式
  shared_ptr<int> p2(new int(1024));	// 正确：使用了直接初始化形式
  ```
出于相同的原因，一个返回 shared_ptr 的函数不能在其返回语句中隐式转换一个普通指针：
  ```
  shared_ptr<int> clone(int p)
  {
      return new int(p); // 错误：隐式转换为 shared_ptr<int>
  }
  ```
## weak_ptr弱引用的智能指针
std::weak_ptr有什么特点呢？与std::shared_ptr最大的差别是在赋值的时候，不会引起智能指针计数增加。

weak_ptr被设计为与shared_ptr共同工作，可以从一个shared_ptr或者另一个weak_ptr对象构造，获得资源的观测权。但weak_ptr没有共享资源，它的构造不会引起指针引用计数的增加。
同样，在weak_ptr析构时也不会导致引用计数的减少，它只是一个静静地观察者。weak_ptr没有重载operator*和->，这是特意的，因为它不共享指针，不能操作资源，这是它弱的原因。
如要操作资源，则必须使用一个非常重要的成员函数lock()从被观测的shared_ptr获得一个可用的shared_ptr对象，从而操作资源。
当我们创建一个weak_ptr时，要用一个shared_ptr来初始化它：
  ```  
auto p = make_shared<int>(42);
weak_ptr<int> wp(p); // wp弱共享p; p的引用计数未改变
  ```
  
  
