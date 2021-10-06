1. Redis的五种数据结构整理  
   
  (1) 简单动态字符串(SDS)  
     SDS等同于C语言中的char*，但它可以存储任意二进制数据，不能像C语言字符串那样以字符'\0'来标识字符串的结束，因此它必须有个长度字段  

     struct sdshdr{
         // 记录buf数组中已使用字节的数量
         // 等于sds所保存字符串的长度
         int len;

         //记录buf数组中未使用字节的数量
         int free;

         //字节数组。用于保存字符串
         char buf[];
     }
    优点：
    - 获取字符串长度的复杂度为O(1)
    - 杜绝缓冲区溢出
    - 减少修改字符串长度时所需要的的内存重分配次数
    - 二进制安全
    - 兼容部分C字符串函数
     
  (2) 链表  
  节点底层结构
  ```c++
  typedef struct listNode{
      //前置节点
      struct listNode *prev;
      struct listNode *next;
      //节点的值
      void* value;
  }listNode;
  ```
  list底层结构
  ```C++
  typedef struct list{
      //表头节点
      listNode *head;
      //表尾节点
      listNode *tail;
      //链表所包含的节点数量
      unsigned long len;
      //节点值复制函数
      void (*dup)(void *ptr);
      //节点值放过函数
      void (*free)(void *ptr);
      //节点值对比函数
      int (*match)(void *ptr,void *key);
  }list;
  ```
  特性：
   - 链表被广泛用于实现Redis的各种功能，比如列表建、发布与订阅、慢查询、监视器等
  - 每个链表节点由一个listNode结构来表示，每个节点都有一个指向前置节点和后置节点的指针，所以Redis的链表实现是双端链表
  - 每个链表使用list结构表示，这个结构带有表头节点指针、表尾节点指针，以及链表长度等信息
  - 因为链表表头的前置节点和表尾的后置节点都指向NULL，所以Redis的链表实现是无环链表
  - 通过为链表设置不同的类型特定函数，Redis的链表可以用于保存各种不同类型的值。

(3)字典
哈希表
``` C++
typedef struct dictht{
    // 哈希表数组
    dictEntry **table;
    //哈希表大小
    unsigned long size;
    // 哈希表大小掩码，用于计算索引值
    //总是等于size-1
    unsigned long sizemark;
    // 该哈希表已有节点的数量
    unsigned long used;
}dicht;
```
特性：
- 字典被广泛用于实现Redis的各种功能，其中包括数据库和哈希键
- Redis中的字典使用哈希表作为底层结构实现，每个字典带有两个哈希表，一个平时使用，另一个仅在进行rehash时使用
- Redis使用MurmurHash算法来计算键的哈希值
- 哈希表使用链地址法来解决键冲突
（4）跳跃表
zskiplistNode
```C++
typedef struct zskiplistNode{
    //后退指针
    struct zskiplistNode *backward;
    //分值 权重
    double score；
    //成员对象
    robj *obj;
    struct zskiplistLevel{
        //前进指针
        struct zskiplistNode *forward;
        //跨度
        unsigned int span;
    }leval[];
}zskiplistNode;
```
zskipList
```C++
typedef struct zskiplist{
    //表头节点和表尾节点
    strcut zskiplistNode *header,*tail;
    //表中节点的数量
    unsigned long length;
    //表中层数最大的节点的层数
    int leval;
}zskiplist;
```
特性：
- 跳跃表是有序集合的底层实现之一
- Redis的跳跃表实现由zskiplist和zskiplistNode两个结构组成，其中zskiplist用于保存跳跃表信息(如表头节点、表尾节点、长度)，而zskiplistNode则用于表示跳跃表节点
- 每个跳跃表节点的层高都是1至32之间的随机数
- 在同一跳跃表中，多个节点可以包含相同的分值，但每个节点的成员对象必须是唯一的。
- 跳跃表中的节点按照分值大小进行排序，当分值相同时，节点按照成员对象的大小进行排序
- 调表是一种实现起来很简单，单层多指针的链表，查找效率很高
- 
![](images/redis的五种数据结构.jpg)
