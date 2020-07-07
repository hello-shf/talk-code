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


#### [08 | 栈：如何实现浏览器的前进和后退功能？](https://time.geekbang.org/column/article/41222)
```java
/**
 * 描述：手写 基于数组的 可扩容的栈
 *
 * @Author shf
 * @Date 2020/7/2 22:40
 * @Version V1.0
 **/
public class MyStack<E>  implements Stack<E> {
    private E[] items;
    private int size = 0;
    private final static int DEFAULT_CAPACITY = 4;
    private static final float DEFAULT_LOAD_FACTOR = 0.25f;
    public MyStack(){
        this.items = (E[]) new Object[DEFAULT_CAPACITY];
        this.size = 0;
    }
    public MyStack(int size){
        this.items = (E[]) new Object[size];
        this.size = 0;
    }
    @Override
    public boolean push(E e) {
        //最大长度
        if(this.size == Integer.MAX_VALUE){
            return false;
        }
        //判断是否需要扩容
        if(this.size == this.items.length){
            int len = items.length;
            //防止长度超过 integer 的最大值
            len = len > Integer.MAX_VALUE / 2 ? Integer.MAX_VALUE : len * 2;
            resize(len);
        }
        this.items[size] = e;
        this.size ++;
        return true;
    }

    /**
     * 接收 newCapacity 满足扩容和缩容，统一逻辑
     * @param newCapacity
     */
    private void resize(int newCapacity){
        E[] newItems = (E[]) new Object[newCapacity];
        for(int i = 0; i<this.size; i++){
            newItems[i] = this.items[i];
        }
        this.items = newItems;
    }

    @Override
    public E peek() {
        return this.items[this.size - 1];
    }

    @Override
    public E pop() {
        if(this.size == 0){
            return null;
        }
        E tmp = this.items[this.size - 1];
        this.size --;
        //防止复杂度震荡（假如是当减少到 len 的 1/ 2就缩容，很容易造成，
        // 当 len = 4，size = 2，此时反复则进行增加和删除，会造成反复的resize）
        //同 hashmap 红黑树转链表 为6 链表转红黑树为 8的道理是一样的
        if(this.size < items.length * DEFAULT_LOAD_FACTOR && this.items.length / 2 != 0){
            this.resize(this.items.length / 2);
        }
        return tmp;
    }

    @Override
    public boolean isEmpty() {
        return this.size == 0;
    }
    public int size(){
        return this.size;
    }

    public int length(){
        return this.items.length;
    }

    public static void main(String[] args) {
        MyStack<Integer> stack = new MyStack<>();
        stack.push(0);
        stack.push(1);
        System.out.println("length = " + stack.length() + " ---- size = " + stack.size() + " ---- value = " + stack.peek());
        stack.push(2);
        stack.push(3);
        stack.push(4);
        System.out.println("length = " + stack.length() + " ---- size = " + stack.size() + " ---- value = " + stack.peek());
        Integer value = stack.pop();
        System.out.println("length = " + stack.length() + " ---- size = " + stack.size() + " ---- value = " + value);
        value = stack.pop();
        System.out.println("length = " + stack.length() + " ---- size = " + stack.size() + " ---- value = " + value);
        value = stack.pop();
        System.out.println("length = " + stack.length() + " ---- size = " + stack.size() + " ---- value = " + value);
        //当满足 len * 0.25 缩容
        value = stack.pop();
        System.out.println("length = " + stack.length() + " ---- size = " + stack.size() + " ---- value = " + value);
//        length = 4 ---- size = 2 ---- value = 1
//        length = 8 ---- size = 5 ---- value = 4
//        length = 8 ---- size = 4 ---- value = 4
//        length = 8 ---- size = 3 ---- value = 3
//        length = 8 ---- size = 2 ---- value = 2
//        length = 4 ---- size = 1 ---- value = 1
    }
}
```

#### [09 | 队列：队列在线程池等有限资源池中的应用](https://time.geekbang.org/column/article/41330)

> 笔记

* 顺序队列
    * 泛指使用数组实现的队列
* 链式队列
    * 泛指使用链表实现的队列
* 基于数组的循环队列
    * 设置两个指针**头指针****尾指针**，用来解决每次remove操作导致的数据搬移，最好的方式就是减少搬移的次数，不需要每次都搬移
    最好的解决方案就是增加一个**head和tail**指针，每次remove也就是出队，只需要将尾指针右移，入队head指针左移
* 实现队列的关键是 判满和判空
    * 顺序队列 指针总是指向下一个空值          满：tail == size      空：head == tail
    * 循环队列需要空出一个位置用于区分空和满   满：(tail+1)%n=head   空：head == tail
    * 链式队列 tail指针总是指向一个null        满：tail == size      空：head == tail
* JUC中公平锁的实现 ArrayListBlockedQueue

#### [10 | 递归：如何用三行代码找到“最终推荐人”？](https://time.geekbang.org/column/article/41440)

> 笔记
* "递归"两个字的理解
    * 去的过程叫做“递”，回的过程叫做“归”
* 递归的核心思想
    * 递归的核心思想就是将重复的问题，不断细化为子问题，将子问题细化为更细粒度的子问题，最终找到最小的子问题得到解决，然后将子问题再不断的汇聚为结果
* 递归的代码实现
    * 自己调用自己
* 递归需要满足的三个条件
    * 一个问题的解可以分解为几个子问题的解
    * 这个问题与分解之后的子问题，除了数据规模不同，求解思路完全一样
    * 存在递归终止条件
* 递归代码的实现思路
    * 写递归代码的关键就是找到如何将大问题分解为小问题的规律，并且基于此写出递推公式，然后再推敲终止条件，最后将递推公式和终止条件翻译成代码
* 递归需要注意的问题
    * 递归代码要警惕堆栈溢出
        * 注意递归深度
        * 注意递归的终止条件
    * 递归代码要警惕重复计算
        * 很合适的一个例子：斐波那锲数列问题，如果用递归解决，代码简单，但是带来的是重复计算，相对用动态规划来解决，递归效率略逊一筹
* 理解递归的终极方法
    * **通过压栈和出栈**理解
    
> 个人认为最难理解的递归：链表反转的递归方案 **递归+引用**

#### [11 | 排序（上）：为什么插入排序比冒泡排序更受欢迎？](https://time.geekbang.org/column/article/41802)

> 笔记

* 问题：插入排序和冒泡排序的时间复杂度相同，都是 O(n2)，在实际的软件开发里，为什么我们更倾向于使用插入排序算法而不是冒泡排序算法呢？
* 分析“排序算法”的角度
    * 最好情况、最坏情况、平均情况时间复杂度
    * 时间复杂度的系数、常数 、低阶
    * 比较次数和交换（或移动）次数
* 排序算法的稳定性
  这个概念是说，如果待排序的序列中存在值相等的元素，经过排序之后，相等元素之间原有的先后顺序不变。
    * 稳定的排序算法：两个相等的值，在排序前后位置不变
    * 不稳定的排序算法：反之
  稳定性貌似感觉没啥问题，那是相对于基本类型，一般都是值比较。但是如果是类似于 Key-Value结构的对象，Key相同不代表值相同。如果改变了前后顺序则意味着可能带来一定的风险。
* 解答开篇
    * 插入排序相比冒泡排序，复制操作只需要一步，而冒泡需要三步。这样，插入排序就能省下两倍于冒泡排序的元素移动时间。


#### [12 | 排序（下）：如何用快排思想在O(n)内查找第K大元素？](https://time.geekbang.org/column/article/41913)

> 笔记

* 归并排序和快速排序都是“分治”思想
* 归并排序
    * 将问题细化为小问题即：将一个数组分为多个小数组，给小数组排序后再合并为大数组
    ![归并排序.jpg](https://github.com/hello-shf/talk-code/blob/master/images/归并排序.png?raw=true)
* 快速排序
    * 找一个基准点pivot，将比这个大的和小的放到pivot的两边，依次处理直到都有序了
* 快排思想在O(n)内查找第K大元素
> 金句
* 分治是一种解决问题的处理思想，递归是一种编程技巧

#### [13 | 线性排序：如何根据年龄给100万用户数据排序？](https://time.geekbang.org/column/article/42038)

> 笔记

* 桶排序
首先，我们来看桶排序。桶排序，顾名思义，会用到“桶”，核心思想是将要排序的数据分到几个有序的桶里，每个桶里的数据再单独进行排序。桶内排完序之后，再把每个桶里的数据按照顺序依次取出，组成的序列就是有序的了。
    * 核心思想就是将数据放到“有序”的桶中（桶之间一定是有序的），然后对每个桶内部数据再进行排序。然后将数据依次桶桶中取出来，即是有序的数据了。
    * 1,0,2,9,14,18,13,12,8,10,19,5,7,16
    {1,0,2}{9,8,5,7}{10,14,13,12}{18,19,16}
                        |                   -- 内部采用快速排序O(nlogn)
    {0,1,2}{5,7,8,9}{10,12,13,14}{16,18,19}
                        |
    0,1,2,5,7,8,9,10,12,13,14,16,18,19
    * 由上可见，使用桶排序的前提
        * 首先，要排序的数据需要很容易就能划分成 m 个桶，并且，桶与桶之间有着天然的大小顺序。这样每个桶内的数据都排序完之后，桶与桶之间的数据不需要再进行排序
        * 其次，数据在各个桶之间的分布是比较均匀的。（类比hash碰撞，都落到一个桶里面）
* 计数排序
当要排序的 n 个数据，所处的范围并不大的时候，比如最大值是 k，我们就可以把数据划分成 k 个桶。每个桶内的数据值都是相同的，省掉了桶内排序的时间。
    * 使用场景
    计数排序只能用在数据范围不大的场景中，如果数据范围 k 比要排序的数据 n 大很多，就不适合用计数排序了。而且，计数排序只能给非负整数排序，如果要排序的数据是其他类型的，要将其在不改变相对大小的情况下，转化为非负整数。
```java
// 计数排序，a是数组，n是数组大小。假设数组中存储的都是非负整数。
public void countingSort(int[] a, int n) {
  if (n <= 1) return;

  // 查找数组中数据的范围
  int max = a[0];
  for (int i = 1; i < n; ++i) {
    if (max < a[i]) {
      max = a[i];
    }
  }

  int[] c = new int[max + 1]; // 申请一个计数数组c，下标大小[0,max]
  for (int i = 0; i <= max; ++i) {
    c[i] = 0;
  }

  // 计算每个元素的个数，放入c中
  for (int i = 0; i < n; ++i) {
    c[a[i]]++;
  }

  // 依次累加
  for (int i = 1; i <= max; ++i) {
    c[i] = c[i-1] + c[i];
  }

  // 临时数组r，存储排序之后的结果
  int[] r = new int[n];
  // 计算排序的关键步骤，有点难理解
  for (int i = n - 1; i >= 0; --i) {
    int index = c[a[i]]-1;
    r[index] = a[i];
    c[a[i]]--;
  }

  // 将结果拷贝给a数组
  for (int i = 0; i < n; ++i) {
    a[i] = r[i];
  }
}
```
    
* 基数排序
    * 类比手机号码排序
    * 首先排第11位
    * 再拍第10位
    * 再排第9位
    ···
    * 直到排到第 1位，则都是有序得了
* 示例
    	   ·	 ·	   ·
a k h	e r g	a k h	a k h
k v o	a k h	w k h	a l i
e r g	w k h	a l i	e r g
a l i	a l i	e r g	k v o
w k h	k v o	k v o	w k h

* 通过上面示例，联想一下：联合索引、最左匹配、最左匹配原则的道理，最左的索引是列是有序的，后面的都是相对前面一个所有有序，但不是绝对有序，所以才有了最左匹配原则
