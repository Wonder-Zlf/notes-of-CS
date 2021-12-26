# Java中数据的引用类型

```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {

    public void test(ListNode head,int num) {
        ListNode aa = head;
        int bb = num;
        
        aa.val = 3; // head.val原本是2
        bb = 3；// num原本是2
        // head和num当前的值是？
        // head.val当前是3，num当前是2
        ListNode cc = aa;
        cc = 4;
    }
}

```

观察上面的例子，我们分别对一个对象head、一个基本类型num复制后，将复制的变量重新赋值。然后，原对象head同时改变，原数据num没有改变。

因为java中的“=”，对基本数据类型（int、double、float、char等）是拷贝，而对其他数据类型（对象）都是“指向”。在ListNode aa = head中，任何计算实际上都是对head做操作，而不是aa。

java中的“指向”，就是对指针的实现。所以如有ListNode cc = aa，任何计算还是对head在操作。

