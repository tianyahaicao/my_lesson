所以 C++11 中引入了智能指针（Smart Pointer），它利用了一种叫做 RAII（资源获取即初始化）的技术将普通的指针封装为一个栈对象。当栈对象的生存周期结束后，会在析构函数中释放掉申请的内存，从而防止内存泄漏。这使得智能指针实质是一个对象，行为表现的却像一个指针。

智能指针主要分为shared_ptr、unique_ptr和weak_ptr三种，使用时需要引用头文件<memory>。C++98 中还有auto_ptr，基本被淘汰了，不推荐使用。而 C++11 中shared_ptr和weak_ptr都是参考boost库实现的。
  
3.1 shared_ptr的初始化
最安全的分配和使用动态内存的方法是调用一个名为 make_shared 的标准库函数。 此函数在动态内存中分配一个对象并初始化它，返回指向此对象的 shared_ptr。与智能指针一样，make_shared 也定义在头文件 memory 中。
  例：
  // 指向一个值为42的int的shared_ptr
  shared_ptr<int> p3 = make_shared<int>(42);

  // p4 指向一个值为"9999999999"的string
  shared_ptr<string> p4 = make_shared<string>(10,'9');

  // p5指向一个值初始化的int
  shared_ptr<int> p5 = make_shared<int>();
我们还可以用 new 返回的指针来初始化智能指针，不过接受指针参数的智能指针构造函数是 explicit 的。因此，我们不能将一个内置指针隐式转换为一个智能指针，必须使用直接初始化形式来初始化一个智能指针：
  shared_ptr<int> pi = new int (1024); // 错误：必须使用直接初始化形式
  shared_ptr<int> p2(new int(1024));	// 正确：使用了直接初始化形式
出于相同的原因，一个返回 shared_ptr 的函数不能在其返回语句中隐式转换一个普通指针：

  shared_ptr<int> clone(int p)
  {
      return new int(p); // 错误：隐式转换为 shared_ptr<int>
  }
