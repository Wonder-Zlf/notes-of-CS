# Array list和Linkedlist的区别



array list底层是object数组，操作包括get获取数组中某个索引位置的元素，remove删除数组中某个元素，indexof查找数组中某个元素的位置等；

array list的add方法是使用无参构造器，修改数组的大小然后加入新的元素；

array list只能在单线程的环境下使用，如果有多个线程改变容器里元素，会抛出ConcurrentModificationException异常；



linkedlist底层是双向链表，创建时就自带了头尾两个指针，可以直接往链表头部、链表尾部插入元素，也支持在一个指定节点前面插入一个新节点；

linkedlist同样是只支持单线程使用，如果迭代期间有线程改变容器，会抛出异常ConcurrentModificationException；

 

array list和linked list的差异本质是连续空间存储和非连续空间存储的差异；

array list底层是object数组实现的，由于数组的地址是连续的，数组支持O(1)随机访问；数组在初始化时需要指定容量；数组不支持动态扩容，像arraylist、vector和stack使用的时候看似不用考虑容量问题，实际上底层是在做扩容，数组扩容的代价比较大，需要开辟一个新数组将数据拷贝进去，数组扩容效率低；

linked list底层使用一个node数据结构，有前后两个指针，双向链表实现；相对数组，链表插入效率高，只需要修改前后两个指针；另外，链表所需要的内容比数组要多，因为要维护前后两个指针，适合删除、插入较多的场景



为什么array list可以直接访问到元素？

因为array list是object数组结构，根据index直接按序查找到元素就可以；

而linkedlist是链表，要找一个元素要通过很多个指针去查找