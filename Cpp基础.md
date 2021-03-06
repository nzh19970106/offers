1. 将可能会被继承的父类的析构函数设置为虚函数，可以保证当我们new一个子类，然后用基类指针指向该子类对象，释放基类指针时可以释放子类的空间，防止内存泄漏。
   C++默认的析构函数不是虚函数是因为虚函数需要额外的虚函数表和虚表指针，占用额外的内存，对于不会用来继承的类设置析构函数为虚函数，会浪费内存。
2. - virtual与静态函数
   (1) static成员不属于任何类对象或类实例，所以即使给此函数加上virtual也是没有任何意义的
   (2) 静态与非静态成员函数之间有一个主要的区别。那就是静态成员函数没有this指针
   虚函数依靠vptr和vtable来处理。vptr是一个指针，在类的构造函数中创建生成，并且只能用this指针来访问它，因为它是类的一个成员，并且vptr指向保存虚函数地址的vtable
   对于静态成员函数，它没有this指针，所以无法访问vptr。
   - const与static函数
   当声明一个非静态成员函数为const时，对this指针会有影响。对于一个Test类中的const修饰的成员函数，this指针相当于Test const*，而对于非const成员函数，this指针相当于Test*。
   而static成员函数没有this指针，所以使用const来修饰static成员函数没有任何意义。
3. static关键字
   静态数据成员存储在静态存储区，只能存储一次，可以节省内存
   static函数只能在声明它的地方可见，不能被其它文件所使用
   static数据成员，静态成员所有对象均可访问，只分配一次内存，只有一份拷贝，值可以更新
   static成员函数，没有this指针，只能访问静态数据成员
4. 普通函数的调用要经过"保存现场、转到被调函数执行、执行完毕返回调用处、恢复现场"，
   内联函数通过代码膨胀来执行，在内联函数调用处复制函数代码。
7. new/delete和malloc/free  
    a. 属性  
    new/delete是C++关键字，需要编译器支持。malloc/free是库函数，需要头文件支持。    
    b. 参数  
    使用new操作符申请内存分配时无须指定内存块的大小，编译器会根据类型信息自行计算。而malloc则需要显式地指出所需内存的尺寸。  
    c. 返回类型  
    new操作符内存分配成功时，返回的是对象类型的指针，类型严格与对象匹配，无须进行类型转换，故new是符合类型安全性的操作符。而malloc内存分配成功则是返回void * ，需要通过强制类型转换将void*指针转换成我们需要的类型。  
    d. 分配失败  
    new内存分配失败时，会抛出bac_alloc异常。malloc分配内存失败时返回NULL。  
    e. 自定义类型  
    new会先调用operator new函数，申请足够的内存（通常底层使用malloc实现）。然后调用类型的构造函数，初始化成员变量，最后返回自定义类型指针。delete先调用析构函数，然后调用operator delete函数释放内存（通常底层使用free实现）  
     malloc/free是库函数，只能动态的申请和释放内存，无法强制要求其做自定义类型对象构造和析构工作。  
     f. 重载  
     C++允许重载new/delete操作符，特别的，布局new的就不需要为对象分配内存，而是指定了一个地址作为内存起始区域，new在这段内存上为对象调用构造函数完成初始化工作，并返回此地址。而malloc不允许重载。  
     g. 内存区域  
     new操作符从自由存储区（free store）上为对象动态分配内存空间，而malloc函数从堆上动态分配内存。自由存储区是C++基于new操作符的一个抽象概念，凡是通过new操作符进行内存申请，该内存即为自由存储区。而堆是操作系统中的术语，是操作系统所维护的一块特殊内存，用于程序的内存动态分配，C语言使用malloc从堆上分配内存，使用free释放已分配的对应内存。 
5. C++中，虚拟内存分为代码段，数据段，BSS段。堆区、映射区以及栈区  
   C++中，内存分为5个区，堆、栈、全局/静态存储区、常量存储区、自由存储区
6. C++ 拷贝赋值函数的形参不能进行值传递，值传递要调用拷贝构造函数，这样形成死循环，栈会变满
7. 指针和引用的区别：
   - 指针有自己的一块空间，而引用只是一个别名
   - 使用sizeof查看指针大小是4，而引用则是被引用对象的大小
   - 指针可以被初始化为NULL，引用必须被初始化且必须是一个已有对象的引用
   - 指针可以随意改变指向，而引用固定在当前对象，不能改变
   - 作为参数传递时，指针需要被解引用才能对对象进行操作，而直接对引用的修改会改变引用所指的对象
   - 动态内存分配的时候需要使用指针避免内存泄漏
8. C++内存存放规则  
   内存连续存放  
   有效对齐值：给定值#pragma pack(n)和结构体中最长数据类型长度中较小的那个  
   (1)结构体的第一个成员的偏移量为0，以后每个成员相对于结构体首地址的offset都是该成员大小与有效对齐值中较小那个的整数倍，如有需要编译器会在成员之间加上填充字节  
   (2)结构体的总大小为有效对齐值得整数倍，如有需要编译器会在最末一个成员之后加上填充字节
   平台原因、性能原因
9.  const_cast、static_cast、dynamic_cast、reinterpret_cast
10. 若类的数据成员是const和引用类型，必须使用初始化列表
11. 智能指针
    - auto_ptr(C++98方案，C++11抛弃)
      采用所有权模式，存在潜在的内存崩溃问题
    - unique_ptr实现独占式占有，保证同一时间内只有一个智能指针可以指向该对象
    - share_ptr实现共享式拥有概念。多个智能指针指向相同的对象，该对象和其相关资源会在“最后一个引用被销毁”时候释放。
    - weak_ptr 一种不控制对象生命周期的智能指针，指向一个share_ptr管理的对象
      用于解决share_ptr相互引用时死锁问题，是对对象的一种弱引用，不会增加对象的引用计数
      不能用weak_ptr直接访问对象的方法，先转为share_ptr


