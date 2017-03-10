---
layout: post
title: "Java 集合概览"
tags:
- Java


---
# 简介
本文将概览所有标准的 Java 集合类型。我们将按照它们可区分的属性与主要用例进行分类。除此之外，我们还将穷举在不同集合类型之间进行数据转换的方法。

# 数组（Arrays）
数组是 Java 语言内置的唯一集合类型，尤其擅长处理预先知道数量上限的元素集。
java.util.Arrays 包含了许多用于处理数组的方法，列举如下：

1. Arrays.asList ——将 array(数组) 转变为 List(列表)，后者可以传值给其他标准的集合构造函数。

2. Arrays.binarySearch ——对有序数组或其分段进行快速查找。

3. Arrays.copyOf ——如果想在扩展数组的同时保存其内容，可调用该方法。

4. Arrays.copyOfRange ——如果需要拷贝整个数组或其分段，可调用该方法。

5. Arrays.deepEquals, Arrays.deepHashCode ——支持嵌套子数组的 Arrays.equals 或 Arrays.hashCode 版本。

6. Arrays.equals ——如果需要比较两个数组是否相等，可调用此方法（不可调用数组自身的equals方法，array.equals 方法未被重写，因此只会比较对数组的引用，而非其内容。）此方法可以与 Java 5 中的boxing(装箱)与 varargs(可变参数)特性并用，以编写类equals方法的简单实现——在比较对象类型之后，将所有类字段都传给 Arrays.equals 即可。

7. Arrays.fill ——将整个数组或其分段赋值为给定的值。

8. Arrays.hashCode ——计算数组内容的 hashcode 值（数组自身的 hashcode 方法无法实现此功能。）此方法可以与 Java 5 中的 boxing(装箱)与 varargs(可变参数)特性并用，以编写类 hashcode 方法的简单实现——将所有类字段都传给 Arrays.hashcode 即可。

9. Arrays.sort ——根据自然排序(natural ordering)规则，对整个数组或其分段进行排序。若要利用已知的 Comparator 对 Object[] 进行排序，也有一对 Arrays.sort 方法。

10. Arrays.toString ——打印数组的具体内容。

如果需要将部分数组（或整个数组）拷贝到另一个已有数组，你需要调用 System.arraycopy 方法，后者会将源数组中指定位置的给定数量的元素拷贝到目的数组的指定位置。通常，这是在 Java 中拷贝数组内容的最快方式（不过，在某些情况下，你也可以检查一下ByteBuffer 批量拷贝的速度是否更快）。

最后，还有一点，任何 Collection(集合) 都可以使用 T[] Collection.toArray( T[] a ) 方法拷贝到数组中。此方法调用的常见模式如下：

    return coll.toArray(newT[coll.size()]);
此方法调用会分配足够大的数组以存储整个集合，因此 toArray 不必要分配很大的数组用于返回。

# 单线程集合(Single-threaded collections)
本部分将重点介绍非线程安全(non-thread-safe)集合。这些集合全都存储于 java.util 包中。其中一些集合类型从 Java 1.0 开始就有了，现在已经不再建议使用(deprecated)，但大多数集合类型从 Java 1.4 开始启用。枚举集合(Enum collections)自 Java 1.5 开始出现，同时具备所有集合类的泛型支持。PriorityQueue 也是从 Java 1.5 开始启用的。非线程安全集合框架的最新成员是自 Java 1.6 起推出的 ArrayDeque。


# Lists(列表)

##1. ArrayList——用处最大的List实现。
包含一个数组与一个int类型，后者代表了数组中首个未使用元素所在的位置。与其他 List 一样，ArrayList 可以在需要的时候进行扩展。此外，元素读取时间固定，在列表尾部更新时成本很小（复杂度是固定的），而在头部更新时成本很高（复杂度是线性增长的），这是因为 ArrayList 不变量——所有元素在底层数组中从索引(index)为0处开始，这意味着在插入元素时，插入位置右边的所有元素都要往右移；在移除元素时，移除位置右边的所有元素都要往左移。由于包含数组，该集合类型易于 CPU 缓存。（不过，也不是特别容易。因为其包含 Objects，而后者其实是指向实际对象的指针）。

## 2. LinkedList——Deque 实现，
每个 Node(结点)由一个值，以及 prev 与 next 指针构成。这意味着，元素读取或更新的复杂度是线性增长的（得益于一些优化，这些方法遍历的范围不会超过列表长度的一半，因此，读取或更新成本最高的元素位于列表的中间位置）。如果打算编写运行更快的 LinkedList 代码，则需要使用 ListIterators。如果想要编写 Queue/Deque 实现(只需读取首尾两个元素)，请转而使用 ArrayDeque。

## 3. Vector  
ArrayList 先前的版本，包含所有同步方法。请尽量使用 ArrayList。


# Queues(队列)/deques(双队列)
ArrayDeque – Deque implementation based on the array (circular buffer) with head/tail pointers. Unlike LinkedList, this class does not implement List interface, which means that you can not access anything except the first and the last elements. This class is generally preferable to LinkedList for queues/deques due to a limited amount of garbage it generates (old array will be discarded on the extensions).

## 1. ArrayDeque
——基于数组(循环缓冲)的 Deque 实现，带有头/尾(head/tail)指针。与 LinkedList 不同，该类并不实现 List 接口，这意味着除了首尾元素，无法读取其他元素。由于该类生成的垃圾较少，在实现队列或双队列时，该类比 LinkedList 更加适合（老数组在扩展时会被抛弃）。

##2. Stack
——后进先出(LIFO)队列。不要在生产代码中使用该类。可用任意 Deque 实现替代之(ArrayDeque 优先)。

##3. PriorityQueue 
基于 priority(优先级)堆的队列。采用自然排序或已知的 Comparator。
其主要属性—— poll/peek/remove/ element方法总是返回队列中最小的余留元素。尽管如此，该队列实现了Iterable，不会按照排好的次序(或任何特定次序)迭代队列。如果只需要队列中的最小元素，该队列往往比 TreeSet 之类的其他排序集合更可取。

# Maps(映射)
## HashMap 
最常见的一种映射实现，将键(key)映射至对应的值(value)，仅此而已。如果想要高效率的hashcode 方法，可以考虑 get/put 方法，它们的复杂度是固定的。

## EnumMap
带有 enum 键的映射。由于已知最大键数量以及内置的 enum 至 int 映射，此类的运行速度往往高于 HashMap。

## Hashtable 
 HashMap 先前的同步化版本，在新产品的代码中尽量用 HashMap 替代之。

## IdentityHashMap
 Map 的超特殊版本，违背了 Map 的通用约定：在比较引用时使用 == 而非调用 Object.equals 方法。这一属性使得 IdentityHashMap 在各种图遍历算法中大显神通,你可以在 IdentityHashMap 中轻易地存储已经处理过的节点，连同一些无关数据。

## LinkedHashMap 
 HashMap 与 LinkedList 的结合体，所有元素的插入次序都存储在 LinkedList 中，也因此，LinkedHashMap 的条目，键以及值都以插入次序迭代。就每个元素的内存消耗量而已，这是成本最高的 JDK 集合类型。

## TreeMap 
基于有序导航型 Map 的红黑树。按照自然次序或给定的键 Comparator 对条目进行排序。此映射类要求 equals 与 Comparable/Comparator.compareTo 的实现必须一致。此类实现了一个 NavigableMap 接口：它允许获得少于或多于给定键的映射；允许获得一个 prev/next 条目(基于键的排序)；允许获得给定范围内的键的映射(这与 SQL 的 BETWEEN 操作符基本对应)以及此方法的许多变体。

## WeakHashMap 
此映射通常用于数据缓存的实现。它将所有键都保存于 WeakReference，这意味着，如果没有指向这些键对象的强引用，这些键就会被垃圾回收。另一方面，值却用强引用保存。因此，你需要确保不存在从值指向键的引用，或者将值也保存在弱引用中：m.put(key, new WeakReference(value))。

# Sets（集合）
## HashSet 
基于带有虚值(每个值的 Object 均相同) HashMap 的 set 实现。与 HashMap 的属性相同。也因为这种实现方式，此类消耗的内存比该数据类型实际需要的内存要多。

## EnumSet
 enum 值的集合。在 Java 中，每个 enum 都映射至一个 int 类型：每个 enum 值映射的 int 都互不相同。这使得 BitSet 之类的集合结构成为可能，每个 bit 都映射到一个不同的 enum 值。此类还存在两种实现—包含单个 long 类型（可存储64个 enum 值，足够覆盖99.9%的用例）的 RegularEnumSet 与包含 long[] 类型的 JumboEnumSet。

## BitSet
 bit 集合。请牢记，可以使用 BitSet 表示整型的稠密集(比如从已知起点开始的 id)。此类使用 long[] 类型存储 bit。

## LinkedHashSet 
与 HashSet 类似，此类基于 LinkedHashMap 类实现。这是以插入次序保存元素的唯一集合类。

## TreeSet 
与 HashSet 类似，此类基于 TreeMap 实例。这是标准 JDK 的单线程阵营中唯一的有序集合。

# java.util.Collections
就像针对数组的 java.util.Arrays 包一样，java.util.Collections 包提供了许多处理集合的好方法。

本文将要介绍的第一组方法会返回集合的各种视图：

1. Collections.checkedCollection / checkedList / checkedMap / checkedSet / checkedSortedMap / checkedSortedSet —— 会返回集合的视图，在运行时检查所添加元素的类型。如果试图添加类型不兼容的元素，会抛出 ClassCastException 异常。该功能能有效防止运行时造型(runtime casts)。

2. Collections.emptyList / emptyMap / emptySet ——当你需要返回不可变的空集合，而又不想分配任何对象时，此类方法便能派上用场。

3. Collections.singleton / singletonList / singletonMap —— 返回带有单个条目的不可变 set/list/map。

4. Collections.synchronizedCollection / synchronizedList / synchronizedMap / synchronizedSet / synchronizedSortedMap / synchronizedSortedSet —— 返回包含所有同步方法的集合视图(低成本又低效率的多线程方法，仍然不支持put-or-update之类的复合动作)。

5. Collections.unmodifiableCollection / unmodifiableList / unmodifiableMap / unmodifiableSet / unmodifiableSortedMap / unmodifiableSortedSet ——返回集合的不可变视图。当需要实现包含任意集合的不可变对象时尤其有用。

6. 本文介绍的第二组方法都因为相同的原因被排除在集合之外：

Collections.addAll ——如果要往一个集合添加一定数量的元素或一个数组的内容，调用此方法。

Collections.binarySearch —— 与 Arrays.binarySearch 对于 arrays(数组)的作用相同。

Collections.disjoint —— 检查2个集合之间不存在共同的元素。

Collections.fill —— 用给定值替换某个列表中的所有元素。

Collections.frequency —— 给定集合中有多少元素与给定对象相等。

Collections.indexOfSubList / lastIndexOfSubList —— 这些方法与 String.indexOf(String) / lastIndexOf(String) 相似，它们会找出给定列表中给定子列表首次或末次出现的情况。

Collections.max / min —— 基于自然排序或 Comparator 找出集合中的最大或最小元素。

Collections.replaceAll —— 在给定列表中用某个元素替代另一个元素。

Collections.reverse —— 颠倒给定列表中的元素次序。如果你打算在列表排序之后立即调用此方法，不如在进行元素排序时使用 Collections.reverseOrder 比较器。

Collections.rotate —— 根据给定距离旋转列表中的元素。

Collections.shuffle ——打乱列表的排序。注意，你可以为此方法提供自己的随机生成器，可以是 java.util.Random，java.util.ThreadLocalRandom 或 java.security.SecureRandom。

Collections.sort —— 根据自然排序或给定的 Comparator 对列表进行排序。

Collections.swap —— 根据给定的位置交换列表中的2个元素(许多开发者都亲自编写此方法)。

# 并发集合
本部分将介绍 java.util.concurrent 包中提供的线程安全(thread-safe)集合类型。这些集合的主要特性在于能确保其方法的原子执行(atomic execution)。不过，不要忘记，涉及多个方法调用的 “add-or-update” 或 “check-then-update” 等复合动作(compound actions)仍然应该同步。因为，在复合动作第一步中查询到的信息在执行第二个步骤之前可能已经失效。

大多数并发集合类型从 Java 1.5 开始引入。ConcurrentSkipListMap / ConcurrentSkipListSet 以及 LinkedBlockingDeque 是在 Java 1.6 开始启用的。在 Java 1.7 中的最近更新为 ConcurrentLinkedDeque 与 LinkedTransferQueue 的加入。

##Lists(列表)
CopyOnWriteArrayList ——列表实现，针对每次更新都创建一个底层数组的新拷贝。这一操作的成本很高，因此，当遍历的次数远大于更新时使用此类比较合适。此集合类型的常见用例为 listeners/observers 集合。

##Queues(队列)/deques(双队列)
ArrayBlockingQueue —— 包含一个数组类的有界阻塞队列。无法调整大小，因此，当向满的队列添加一个元素时，该方法调用会遭到阻塞，直到另一个线程从该队列中提取出了一个元素。

ConcurrentLinkedDeque / ConcurrentLinkedQueue ——基于链表(linked list)的无界队列/双队列。往该队列中添加元素不会遭到阻塞，但是要求此类集合的使用者(consumer)必须与其产出者(producer)保持至少相同的工作效率，否则就会出现内存耗尽的情况。此类还相当依赖 CAS (compare-and-set) 操作。

## DelayQueue 
由 Delayed(延迟) 元素组成的无界阻塞队列。其元素只有在延迟过期之后才能从队列中剔除。队首是剩余延迟值最小(包括负值，代表延迟已经过期)的元素。当想要实现一个由延迟任务组成的队列时(无需手动实现，使用 ScheduledThreadPoolExecutor 即可)，此队列便能派上用场。

LinkedBlockingDeque / LinkedBlockingQueue ——基于链表(linked list)的可选有界(可以在创建时指定最大容量，也可以不指定)队列/双队列。以 ReentrantLock-s 为空/满条件。

## LinkedTransferQueue 
基于链表(linked list)的无界队列。除了普通的队列操作，此类还拥有一组传递方法(transfer methods)，允许产出者直接向等待中的使用者发送消息，从而解决了在队列中存储元素的需求。这是一个基于 CAS 操作的无锁(lock-free)集合类型。

PriorityBlockingQueue —— PriorityQueue 的无界阻塞队列版。

##SynchronousQueue 
不带任何内部容量的阻塞队列。因此，任何插入请求必须等待对应的删除请求，反之亦然。如果你不需要 Queue 接口，同样的功能可以通过 Exchanger 实现。

# Maps(映射)
ConcurrentHashMap ——对get操作全并发、对put操作可配置并发的散列表(hash table)。该并发等级可通过构造函数参数(默认为16) concurrencyLevel 进行调整，该参数定义了映射内的划分情况。在put操作中只有更新后的划分会被锁定。请牢记，此映射类型并不是 HashMap 算法的线程安全替代品 ——任何对此映射(或其他并发集合类)的 “get-then-put” 序列方法调用都必须是外部同步的。

ConcurrentSkipListMap ——基于跳跃表(skip list)的 ConcurrentNavigableMap 实现。本质上，此集合类可以作为 TreeMap 的线程安全替代物使用。

# Sets（集合）
ConcurrentSkipListSet ——采用 ConcurrentSkipListMap 类进行存储的并发集合。

CopyOnWriteArraySet ——采用 CopyOnWriteArrayList 类进行存储的并发集合。

# 另请参阅
原始类型集合类：Trove 库—— 对 Trove 库的概述，Trove 库包含了诸多存储着 Java 原始类型的集合类(与大多数 JDK 集合类中的 Objects 不同)。

常见 Java 数据类型的内存消耗 ——第一部分——介绍了 enums 以及 EnumMap / EnumSet / BitSet / ArrayList / LinkedList / ArrayDeque 等类的内存消耗情况。

常见 Java 数据类型的内存消耗 ——第二部分——介绍了 HashMap / HashSet, LinkedHashMap / LinkedHashSet, TreeMap / TreeSet 以及 Java 7 中 PriorityQueue JDK 类的内存消耗情况，以及它们对应的Trove替代物。

# 总结
以下是所有 JDK 集合类的简单总结:


<table>
    <tr>
        <td></td>
        <td>Single threaded (单线程)</td>
        <td>Concurrent (并发)</td>
    </tr>
    <tr>
        <td>Lists(列表)</td>
        <td>ArrayList – 基于数组的泛型

LinkedList – 请勿使用

Vector – 已弃用</td>
        <td>CopyOnWriteArrayList – 极少更新，常用于遍历</td>
    </tr>
    <tr>
        <td>Queues / deques(队列/双队列)</td>
        <td>ArrayDeque – 基于数组的泛型

Stack – 已弃用

PriorityQueue – 有序检索操作</td>
        <td>ArrayBlockingQueue – 有界阻塞队列

ConcurrentLinkedDeque / ConcurrentLinkedQueue – 无界链式队列 (CAS)

DelayQueue – 由延迟元素构成的队列

LinkedBlockingDeque / LinkedBlockingQueue – 可选有界链式队列 (locks)

LinkedTransferQueue – 可传输元素且不进行存储

PriorityBlockingQueue – 并发 PriorityQueue

SynchronousQueue – Queue 接口的交换器</td>
    </tr>
    <tr>
        <td>Maps(映射)    </td>
        <td>HashMap – 泛型映射

EnumMap – 枚举键

Hashtable – 已弃用

IdentityHashMap – 用 == 对比的键

LinkedHashMap – 保持插入次序

TreeMap – 有序键

WeakHashMap – 有益于缓存</td>
        <td>ConcurrentHashMap –泛型并发映射

ConcurrentSkipListMap – 有序并发映射</td>
    </tr>
    <tr>
        <td>Sets(集合)</td>
        <td>HashSet – 泛型集合

EnumSet – 枚举集合

BitSet – bits/dense 整型集合

LinkedHashSet – 保持插入次序

TreeSet – 有序集合</td>
        <td>ConcurrentSkipListSet – 有序并发集合

CopyOnWriteArraySet – 极少更新，常用于遍历</td>
    </tr>

       
















    



</table>
 
