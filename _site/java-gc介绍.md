# GC介绍

垃圾回收并不是JAVA的首创,早在Lisp就有垃圾回收机制.有了垃圾回收机制,我们不在需要考虑手动回收对象内存,但是为了更好的利用GC机制,我们需要对他了解的多一些,这篇blog将简单较少关于JAVA GC.
GC主要完成3件事:
* 哪些内存需要回收
* 什么时候回收
* 如何回收

了解GC的原理能够帮助我们排查内存溢出,内存泄漏等问题,当垃圾收集成为系统达到更高并发量的瓶颈时,我们就需要进行必要的监控和调节了.

### 对象已死?

#### 引用次数算法

这是一种比较简单直观的GC算法, 给每个对象添加一个引用计数器,当对象被引用一次则计数器值+1,当引用失效时-1.任何时刻计数器为0的对象就是不可能再被使用的.这种算法简单直观,但是在JAVA中并不使用这种算法,因为这种算法存在不足之处. 举个例子: `object1.instance = object2; object2.instance = object1;`这里有两个对象,这两个对象只有这两个引用,他们互相引用导致计数器始终不为0,无法通知GC收集器回收他们.引用次数算法并不能解决对象依赖的问题.

#### 可达性分析算法

简单来说就是有一个GC Root, 从GC Root出发,向下搜索,搜索所走过的路径成为引用链, 当一个对象到GC Root没有任何引用链相连,则说明对象不可用. 在JAVA中, 可以作为GC Root的对象包含下面几种:

* 虚拟机栈(本地变量表)中引用的对象
* 方法区中类静态属性引用的对象
* 本地方法栈中引用的对象

### 垃圾回收算法

#### 标记-清除算法
首先标记所有需要回收的对象, 在标记完成以后统一回收所有被标记的对象.这个算法比较简单直观,但是有一些不足之处: 1. 效率比较低 2. 清除完以后会造成一堆不连续的内存,导致内存空间碎片化,如果程序运行过程中需要分配大的对象时会因为找不到合适的内存空间而不得不触发一次GC.

#### 复制算法
这个算法比较常用,它将内存分成两份,当一块内存用完了,就将还存活的对象复制到另外一块上面然后再把剩下的空间清空,这样就不用考虑内存碎片化的问题,只需要简单的移动一下堆顶指针即可,实现简单效率高,但是内存消耗高,存在一定的浪费.

这种算法被使用到了新生代的垃圾回收中. 新生代中的对象基本上都是早死,所以不按照1:1的方式分割内存空间,而是将内存分为较大的一个Eden空间和两个较小的survivor空间.当Eden空间满的时候会进行一次Minor GC,垃圾回收后的对象将复制到survivor1区,并清空Eden区,当survivor1区也满的时候也会触发一次Minor GC, 再将survivor1区GC后的对象复制到survivor2区并清空survivor1区和Eden区.之后发生在Eden区的Minor GC将剩下来的对象复制到survivor2区, 直到survivor2区也满了,在survivor2区进行一次Minor GC, 再将剩下来的对象复制到survivor1区. 就这样往返15次(这个可以自定义设置,默认15次)以后将survivor区GC后剩下来的对象复制到老生代,再清空survivor区的内存空间.
Eden区和Survivor的比例是8:1,也就是说会有10%的内存空间被浪费.

#### 标记整理算法

相对于标记清除算法, 标记整理算法是一个优化算法. 在老生代上有人提出了标记整理算法, 标记要被回收的对象,并直接进行清理, 只是清理完以后让所有存活的对象都想一端移动,然后直接清理掉端边界以外的内存.

#### 分代收集算法
分代收集算法是在新生代上使用复制算法,在老生代上使用标记整理算法来进行回收

