#### [05 | 数组：为什么很多编程语言中数组都从0开始编号？](https://time.geekbang.org/column/article/40961)

> 笔记

* 数组特点
    * 线性表-他利用一组连续的内存空间，来存储一组具有相同类型的数据。
    * 连续且固定的内存空间
        * 数据在定义了数组长度后，就会申请一个固定的并且是连续的内存空间。
            * 为什么是连续的？
                * 因为数组要做到随机访问
            * 为什么是固定的？
                * 因为是连续的，如果不固定，假如申请的内存空间是0-1.| 0 | 1 |但是后面2位置被占用了，怎么扩容？所以也没法扩容。
    * 定长
        * 因为连续且固定的内存空间
* 访问时间复杂度 O(1) **不代表查询时间复杂度也是O(1)**
    * O(1)指的是通过下标访问
    * 如果是查询数组的数值，同样需要遍历。O(n)。如果是排序的通过二分法查找也需要O(logn)
* 删除-平均时间复杂度O(n)
    * 类比JVM的标记清楚算法
> 金句
* 很多时候我们并不是要去死记硬背某个数据结构或者算法，而是要学习它背后的思想和处理技巧，这些东西才是最有价值的

#### [06 | 链表（上）：如何实现LRU缓存淘汰算法?](https://time.geekbang.org/column/article/41013)

> 笔记

* 内存置换算法
    * FIFO 先进先出
    * LRU : 最近最少使用
    * LFU : 最少使用
* 链表
    * 双向链表
        * LinkedList 双向链表
    * 单向链表
    * 循环链表
[详见](https://www.cnblogs.com/hello-shf/p/11304615.html)
    
    
#### [07 | 链表（下）：如何轻松写出正确的链表代码？](https://time.geekbang.org/column/article/41013)

* 链表最终要的是引用问题
    * 关于这个最好的一个问题就是**链表反转**能真的懂了链表反转就懂了链表和引用了。
```java
/**
 * 描述：手写链表
 *
 * @Author shf
 * @Date 2020/7/1 23:15
 * @Version V1.0
 **/
public class LinkList<E> {
    private class Node{
        E e;
        Node next;
        public Node(E e){
            this(e, null);
        }
        public Node(){
            this(null, null);
        }
        public Node(E e, Node next){
            this.e = e;
            this.next = next;
        }
    }
    private int size;
    //虚拟头--哨兵
    private Node dummyHead;
    public LinkList(){
        this.dummyHead = new Node(null, null);
        this.size = 0;
    }
    private void add(int index, E e){
        if(index > this.size)
            throw new IllegalArgumentException("参数Index越界");
        Node pre = dummyHead;
        for(int i = 0; i<index; i++){
            pre = pre.next;
        }
        pre.next = new Node(e, pre.next);
        this.size ++;
    }
    public void add(E e){
        this.addLast(e);
    }
    public void addFirst(E e){
        this.add(0, e);
    }
    public void addLast(E e){
        this.add(this.size, e);
    }
    @Override
    public String toString(){
        StringBuilder res = new StringBuilder();

        Node cur = dummyHead.next;
        while(cur != null){
            res.append(cur.e + "->");
            cur = cur.next;
        }
        res.append("NULL");
        res.append("----size=" + size);
        return res.toString();
    }

    public static void main(String[] args) {
        LinkList<Integer> list = new LinkList<>();
        list.add(1);
        list.add(2);
        list.add(3);
        list.add(4);
        list.add(5);
        System.out.println(list);
    }
}
```