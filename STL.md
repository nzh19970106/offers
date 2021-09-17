### C++关联容器
- map 红黑树
- unordered_map 哈希表
  解决散列冲突的方法：
  - 开放地址法、一旦发生了冲突，就去寻找下一个空的散列地址，只要散列表足够大，
    空的散列地址总能找到，并将记录存入
  - 链地址法、产生hash冲突后在存储数据后面加上一个指针，指向后面冲突的数据。
  - 公共溢出区法、建立一个特殊的存储空间，专门存放冲突的数据，适用于数据和冲突较少的情况
  - 再散列法、准备若干个hash函数，如果使用第一个hash函数发生了冲突，就再使用第二个，以此类推  
  STL线程安全：  
  - 多个读取者是安全的  
  - 对不同容器的多个写入者是安全的
  1. 每次调用容器的成员函数的期间需要锁定  
  2. 每个容器返回迭代器的生存期需要锁定  
  3. 每个容器在调用算法的执行期需要锁定 
1. STL sort
   - 为什么元素个数较少直接采用插入排序？
    因为当元素个数少于阈值，该子序列都有相当程度的排序，但尚未完全排序，
    插入排序有个很好的特性是对于几近排序完的数据，有很好的表现。
   - 为什么递归深度超过阈值直接采用的堆排序
    递归太深容易栈溢出问题，为了避免该问题，当递归太深时直接调用堆排序，
    原因是因为堆排序是原地排序，并且运行时间复杂度是nlogn，速度很快。
   - 为什么要用三中值查切分点？
    快速排序有个很大的缺点就是当切分点选择不合理，数据不能切分平衡的话，
    导致性能最差可能退化到O(n^2)，使用三种值确定切分点，可以较好的避免缺点的出现
2. C++11的一些特性
   - auto关键字：编译器可以根据初始值自动推导出类型。但是不能用于函数传参以及数组类型的推导
   - nullptr关键字：nullptr是一种特殊类型的字面值，可以被转换成任意其它的指针类型，
     而NULL一般被认为宏定义为0，在遇到重载时可能出现问题。
   - 智能指针：C++11新增了share_ptr、weak_ptr等类型的智能指针，用于解决内存管理的问题
   - 初始化列表：使用初始化列表来对类进行初始化
   - 右值引用：基于右值引用可以实现移动语义和完美转发，消除两个对象交互时不必要的对象拷贝，
     节省运算存储资源，提高效率
   - atomic原子操作用于多线程资源互斥操作
   - 新增STL容器array以及tuple
3. STL迭代器删除元素(主要考察迭代器失效的问题)
   - 对于序列容器vector，deque，使用erase(iterator)后，后边的每个元素的迭代器都会失效，
     但是后边每个元素都会往前移动一个位置，但是erase会返回下一个有效迭代器
   - 对于关联容器map和set，使用erase(iterator)后，当前元素的迭代器失效，
     但是其结构是红黑树，后续迭代器不会受到影响
   - 对于list，使用了不连续分配内存，它的erase方法会返回下一个有效的iterator
二叉查找树：
    B树，如果经常访问的数据离根节点很近，而B树的非叶子节点存储关键字数据的地址，这种数据检索速度要比B+树更快。
    B+树：  
    - 层级少，查询速度快；
    - 关键字数据地址存在叶节点上，每次查询次数相同，查询速度稳定；
    - 叶子节点构成有序链表，数据紧密性高，查询大小区间的数据更方便，缓存的命中率高
    - 遍历只需要遍历叶节点，扫描速度快
    红黑树高度2log(n+1);B+树logt(n+1)/2+1
1. 优先队列
   优先队列可以用vector或queue来实现(list不能实现queue，list迭代器不是任意存取iterator，而pop中用到堆排序时要求random access iterator)
   优先队列队首指向最后，队尾指向最前面
   priority_queue<int> q; //元素从大到小出队
   priority_queue<int,vector<int>,greater<int>> q; //元素从小到大出队
   - 自定义优先级
   ```
   struct cmp{
       operator bool()(int x,int y)
       {
           return x>y; //x小优先级高
       }
   };
   ```
   priority_queue<int,vector<int>,cmp> q; //定义方法
   - 结构体声明方式
   ```
   struct node{
       int x,y;
       friend bool operator <(node a,node b)
       {
           return a.x>b.x;
       }
   };
   ```
   priority_queue<node> q; //定义方法
   ```
   struct ListNode{
       int val;
       ListNode* next;
       ListNode():val(0),next(nullptr){}
       ListNode(int x):val(x),next(nullptr){}
       ListNode(int x,ListNode* next):val(x),next(next){}
   };
   ```
   ```
   struct TreeNode{
       int val;
       TreeNode* left;
       TreeNode* right;
       TreeNode():val(0),left(nullptr),right(nullptr){}
       TreeNode(int x):val(x),left(nullptr),right(nullptr){}
       TreeNode(int x,TreeNode* left,TreeNode* right):val(x),left(left),
       right(right){}
   };
   ```
   node* st=(node*)malloc(sizeof(node)); //malloc 指针

   - 仿函数
   vector<vector<int>> intervals;
   sort(intervals.begin(),intervals.end(),[](vector<int>&a,
   vector<int>&b)
   {
       return a[1]<b[1];
   });

   - map 比较string长度
   struct cmpByKeyLength{
       bool operator()(const string& k1,const string& k2){
           return k1.length()<k2.length();
       }
   };

   - map 按value排序
   typedef pair<string,int> PAIR;
   bool cmp_by_value(const PAIR& lhs,const PAIR& rhs)
   {
       return lhs.second<rhs.second;
   }
   struct cmpByValue{
       bool operator()(const PAIR& lhs,const PAIR& rhs){
           return lhs.second<rhs.second;
       }
   };
   map<string,int> name_score_map;
   vector<PAIR> name_score_vec(name_score_map.begin(),name_score_map.end());
   sort(name_score_vec.begin(),name_score_vec.end(),CmpByValue());
   sort(name_score_vec.begin(),name_score_vec.end(),cmp_by_value);

   - vector 求最大值
   auto maxPosition=max_element(vec.begin(),vec.end());
   cout<<*maxPosition;

