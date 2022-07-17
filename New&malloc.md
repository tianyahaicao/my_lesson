1. malloc()函数
1.1 malloc的全称是memory allocation，中文叫动态内存分配。
原型：extern void *malloc(unsigned int num_bytes);
说明：分配长度为num_bytes字节的内存块。如果分配成功则返回指向被分配内存的指针，分配失败返回空指针NULL。当内存不再使用时，应使用free()函数将内存块释放。

1.2 void *malloc(int size);
说明：malloc 向系统申请分配指定size个字节的内存空间，返回类型是 void* 类型。void* 表示未确定类型的指针。C,C++规定，void* 类型可以强制转换为任何其它类型的指针。 　　
备注：void* 表示未确定类型的指针，更明确的说是指申请内存空间时还不知道用户是用这段空间来存储什么类型的数据（比如是char还是int或者...）

1.3 free
void free(void *FirstByte)： 该函数是将之前用malloc分配的空间还给程序或者是操作系统，也就是释放了这块内存，让它重新得到自由。

1.4注意事项
1）申请了内存空间后，必须检查是否分配成功。
2）当不需要再使用申请的内存时，记得释放；释放后应该把指向这块内存的指针指向NULL，防止程序后面不小心使用了它。
3）这两个函数应该是配对。如果申请后不释放就是内存泄露；如果无故释放那就是什么也没有做。释放只能一次，如果释放两次及两次以上会出现错误（释放空指针例外，释放空指针其实也等于啥也没做，所以释放空指针释放多少次都没有问题）。
4）虽然malloc()函数的类型是(void *),任何类型的指针都可以转换成(void *),但是最好还是在前面进行强制类型转换，因为这样可以躲过一些编译器的检查。

1.5  malloc()到底从哪里得到了内存空间？
答案是从堆里面获得空间。也就是说函数返回的指针是指向堆里面的一块内存。操作系统中有一个记录空闲内存地址的链表。当操作系统收到程序的申请时，就会遍历该链表，然后就寻找第一个空间大于所申请空间的堆结点，然后就将该结点从空闲结点链表中删除，并将该结点的空间分配给程序。

2. new运算符

2.1 C++中，用new和delete动态创建和释放数组或单个对象。

动态创建对象时，只需指定其数据类型，而不必为该对象命名，new表达式返回指向该新创建对象的指针，我们可以通过指针来访问此对象。
int *pi=new int;
这个new表达式在堆区中分配创建了一个整型对象，并返回此对象的地址，并用该地址初始化指针pi 。

2.2 动态创建对象的初始化

动态创建的对象可以用初始化变量的方式初始化。
int *pi=new int(100); //指针pi所指向的对象初始化为100
string *ps=new string(10,’9’);//*ps 为“9999999999”

如果不提供显示初始化，对于类类型，用该类的默认构造函数初始化；而内置类型的对象则无初始化。
也可以对动态创建的对象做值初始化：
int *pi=new int( );//初始化为0
int *pi=new int;//pi 指向一个没有初始化的int
string *ps=new string( );//初始化为空字符串 （对于提供了默认构造函数的类类型，没有必要对其对象进行值初始化）





区别：
1、new 是c++中的操作符，malloc是c 中的一个函数
2、new 不止是分配内存，而且会调用类的构造函数，同理delete会调用类的析构函数，而malloc则只分配内存，不会进行初始化类成员的工作，同样free也不会调用析构函数
3、new可以自动计算所需要的字节大小。而malloc必须人为的计算出所需要的字节数。
4、分配内存成功的话，new返回指定类型的指针。而malloc返回void指针，可以在返回后强行转换为实际类型的指针。

class Obj

{

public :

Obj(void){ cout << “Initialization” << endl; }

~Obj(void){ cout << “Destroy” << endl; }

void      Initialize(void){ cout << “Initialization” << endl; }

void      Destroy(void){ cout << “Destroy” << endl; }

};

void UseMallocFree(void)

{

Obj    *a = (obj *)malloc(sizeof(obj));     // 申请动态内存

a->Initialize();                          // 初始化

//…

a->Destroy();     // 清除工作

free(a);          // 释放内存

}

void UseNewDelete(void)

{

Obj    *a = new Obj;    // 申请动态内存并且初始化

//…

delete a;             // 清除并且释放内存

}
