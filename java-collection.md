# JAVA的集合

趁着周末写点关于JAVA的数据结构.在工作中我们经常要用到各种数据结构, 我们常用的主要分为Collection和MAP.

### Collection

Collection主要分为几部分:

* List
* Queue
* Set

Collection是一个个独立元素的序列,这些元素的都服从多条规则,比如说List必须按照顺序保存元素, Set的元素都是唯一的且无法保证元素顺序,Queue是以队列的方式保证元素顺序.

#### List

首先讲讲List, List类似数组,我们知道JAVA里面的数组的容量是固定的(当然也有办法扩容), 但是List的容量能够动态的进行扩容,他是数组的一种变种.在List中常用的有ArrayList, LinkedList. List提供了一些比较常用的方法如: 

    + add: 添加元素
    + remove: 删除元素
    + contains: 判断是否包含某个元素
    + indexOf: 元素的索引编号
    + subList: 获得List的子List
    + clear: 清空List
    + removeAll: 移除List元素
    + retainAll: 求两个List的交集元素
    + size: 元素个数

* ArrayList: 优势在于随机访问元素, 但是他也有缺点, 因为ArrayList是基于数组的拷贝扩容方式,所以他的插入和移除会比较慢.ArrayList是如何做到扩容的呢? 他本质上是一个数组, 我们可以定义一个ArrayList的默认容量n,在底层就创建了一个长度为n的数组A, 当元素数量到达了n,在添加元素的时候将生成一个长度为2n的数组B,并将数组A中的元素拷贝到数组B在完成添加操作,这就是为什么ArrayList插入删除会比较慢了. 在并发的情况中, ArrayList是线程不安全的.
* LinkedList: 基于链表,元素的添加删除操作更加快捷,只要移动指针就行了,同时能够很好的支持顺序访问, 但是随机访问比较慢.LikedList添加了一些额外的操作方法,如: getFirst(getFirst和element方法效果一致), addFirst, addLast, removeList, removeFirst(removeFirst和remove方法效果一致,都是移除第一个元素). 同样,在并发的情况中, LinkedList也是线程不安全的.


#### Set

Set的最大特点就是元素保证唯一, 如何保证元素唯一呢? 那就需要实现Set元素的equal方法了.Set常用的有HashSet, TreeSet, LinkedSet,SortedSet.

* HashSet: HashSet方便快速查找, 存放到HashSet中的元素必须实现hashCode()方法.讲讲hashCode的机制,hashCode方法返回的是一个整数,你就会想返回一个整数有什么用呢?实际上这个整数代表对象的Hash码(对象特征), 对象的物理地址, 字符串内容或是计算出来的数值.在设计hashCode()方法时最重要的是要考虑: 无论何时,对同一个对象调用hashCode()都应该生成同样的值,也就是说不能使用时间因素,不能引入外部变量,不能依赖易变数据.在Effective Java中提供了下面的方案: 
    - 给int变量result赋值非0常量
    - 为对象内的每隔有意义的域f(通常是用来判定equeals()的域)计算出一个int散列码c(这个域的hashCode值)
    - 合并计算散列码: result = 37*result + c;
    - 返回result
    - 检查相同的对象确保有相同的hashCode值

hashCode可能不唯一,但是hashCode加上equal就能够确定元素是否相同了.所以,我们重写equals方法一定要重写hashCode方法, 反之亦然.
* TreeSet: 保持次序的Set,底层是树结构, 使用他可以在Set中提取有序序列,元素必须实现Comparable接口.
* LinkedSet: 具有HashSet的查询速度,且内部使用链表按照插入顺序维护元素顺序,元素需要定义hashCode方法.