---
layout:     post
title:      "集合知识点（一）之ArrayList"
subtitle:   " \"ArrayList源码解析\""
date:       2018-07-05 12:00:00
author:     "Mr.Xu"
header-img: "img/u=821233265,3409953519&fm=27&gp=0.jpg"
#catalog: true
tags:
    - java
---

> “Man is not made for defeat .A man can be destroyed but not defeated！ ”

```java
 //版本jdk1.8

 // Resizable-array implementation of the <tt>List</tt> interface.  Implements
 // all optional list operations, and permits all elements, including
 // <tt>null</tt>.  In addition to implementing the <tt>List</tt> interface,
 // this class provides methods to manipulate the size of the array that is
 // used internally to store the list.  (This class is roughly equivalent to
 // <tt>Vector</tt>, except that it is unsynchronized.)

 // 这是一个实现了List接口的可变长度的数组。实现了list接口中的所有方法，允许存放所有的
 // 元素，包括null。除了实现list接口，还提供了操作数组长度的方法。
 //（这个类和vector大致相当，除了它是线程不安全的） */
```

- 要点：数组，可以存放重复的元素，包括null，非线程同步的。

```java
// <p>The <tt>size</tt>, <tt>isEmpty</tt>, <tt>get</tt>, <tt>set</tt>,
// <tt>iterator</tt>, and <tt>listIterator</tt> operations run in constant
// time.  The <tt>add</tt> operation runs in <i>amortized constant time</i>,
// that is, adding n elements requires O(n) time.  All of the other operations
// run in linear time (roughly speaking).  The constant factor is low compared
// to that for the <tt>LinkedList</tt> implementation.
 
// size(),isEmpty(),get(),set()方法使用的一个常量时间（也就是说这些操作与元素的个数无关，操作的时间为
// o(1)）。add操作花费恒定分摊时间，也就是说插入n的元素的时间为o（n），其实分摊之后，也就相当于插入一
// 个元素的时间为o（1）。 粗略的来说本类的其他操作都能在线性的时间内完成。（也就是说这些操作与元素的个
// 成线性关系，操作的时间复杂度o（n）。常数因子跟linkedlist相比是较低的。 */
```

- 要点：底层为数组结构，相对于LinkedList效率较高。

```java
// <p>Each <tt>ArrayList</tt> instance has a <i>capacity</i>.  The capacity is
// the size of the array used to store the elements in the list.  It is always
// at least as large as the list size.  As elements are added to an ArrayList,
// its capacity grows automatically.  The details of the growth policy are not
// specified beyond the fact that adding an element has constant amortized
// time cost.
 
// 每个ArrayList的实例对象都有一个容量（capacity）。这个容量就是这个list中用来存储元素的数组的大小。
// 它至少和list的大小一样大。当有元素被增加到集合中时，它的容量会自动增加。除了要求添加一个元素的效
// 率为“恒定分摊时间”，对于具体实现的细节没有特别的要求。


// <p>An application can increase the capacity of an <tt>ArrayList</tt> instance
// before adding a large number of elements using the <tt>ensureCapacity</tt>
// operation.  This may reduce the amount of incremental reallocation.
 
// 在大批量插入元素前，使用ensureCapacity（）方法来增加集合的容量。
// 这或许能够减少扩容增加量的大小。


 // <p><strong>Note that this implementation is not synchronized.</strong>
 // If multiple threads access an <tt>ArrayList</tt> instance concurrently,
 // and at least one of the threads modifies the list structurally, it
 // <i>must</i> be synchronized externally.  (A structural modification is
 // any operation that adds or deletes one or more elements, or explicitly
 // resizes the backing array; merely setting the value of an element is not
 // a structural modification.)  This is typically accomplished by
 // synchronizing on some object that naturally encapsulates the list.
 
// 注意这个实现类是非同步的。如果有多个线程同时操作一个ArrayList的实例。
// 然后，至少有一个线程修改了list的结构，就必须在外部保证它的线程同步。

// 结构修改指的是增加或者删除一个或多个映射；如果仅仅是更改已经存在的
// key和value值，不算做结构修改）。这通常需要在一个被封装好的list对象上，
// 使用同步进行操作。

 
 // If no such object exists, the list should be "wrapped" using the
 // {@link Collections#synchronizedList Collections.synchronizedList}
 //  method.  This is best done at creation time, to prevent accidental
 // unsynchronized access to the list:<pre>
 //  List list = Collections.synchronizedList(new ArrayList(...));</pre>
 
// 如果没有这样的对象存在，那么就需要使用Collections.synchronizedList方法来
// 包装这个list对象，而且最好是在创建对象的时候就进行包装，这是为了预防
// 对这个list对象进行一些线程不同步的操作。
// 举个例子：List list = Collections.synchronizedList(new ArrayList(...)); */
```

- 要点1：尽管有一个线程安全的类Vector和ArrayList结构类似，但是我们在需要保证线程安全时依然不会使用Vector这个类（过时的类），而是使用 Collections.synchronizedList（）方法来包装得到一个线程安全的list对象。

```java
// <p><a name="fail-fast">
// The iterators returned by this class's {@link #iterator() iterator} and
// {@link #listIterator(int) listIterator} methods are <em>fail-fast</em>:</a>
// if the list is structurally modified at any time after the iterator is
// created, in any way except through the iterator's own
// {@link ListIterator#remove() remove} or
// {@link ListIterator#add(Object) add} methods, the iterator will throw a
// {@link ConcurrentModificationException}.  Thus, in the face of
// concurrent modification, the iterator fails quickly and cleanly, rather
// than risking arbitrary, non-deterministic behavior at an undetermined
// time in the future.
 
	
// 该类的集合视图方法返回的迭代器是fail-fast机制的:在迭代器被创建后,如果list对象   被结构化修改后,无论
// 在何时,使用何种方法(除了迭代器本身的remove方法)来修改它,都会抛出
// ConcurrentModificationException.
// (也就是ArrayList的expectedModCount和   modCount对不上的时候会抛出上述异常.)因此,面对并发修改操作
// 时, 迭代器会迅速且清晰地报错.而不是冒着在不确定的时间做不确定的操作的风险.	
 
// =======================fail-fast机制====================
//   “快速失败”也就是fail-fast，它是Java集合中的一种错误检测机制。某个线程在对collection进行迭代时，
// 不允许其他线程对该collection进行结构上的修改。例如：
//  假设存在两个线程（线程1、线程2），线程1通过Iterator在遍历集合A中的元素，在某个时候线程2修改了集合
// A的结构（是结构上面的修改，而不是简单的修改集合元素的内容），那么这个时候程序就会抛出
//   ConcurrentModificationException 异常，从而产生fail-fast。*/
```

-    要点：==========fail-fast机制=============

我们先看一个《阿里巴巴java开发手册中》中相关的小例子：

![img](/img/2018-7-5-arraylist/v2-0257062c3de4188b1752533f6848dbf5_b.jpg)

这里把“1”换位“2”后，就会触发集合的fail-fast机制，从而抛出ConcurrentModificationException异常，看注释可以明白这里抛出异常是因为有两个线程都在对集合进行操作，也就是迭代器的一个线程，加上list自己的一个线程，并且其中list自己的线程修改了集合的结构（也就是remove操作）。

那么问题来了？为什么删除“1”的时候就没有触发fail-fast机制呢？这就跟迭代器的源码有关系了。

详情请参见下列链接的详细介绍：[阿里巴巴java开发手册的建议 集合为什么不能 remove](https://blog.csdn.net/qq_31452013/article/details/73088076)

更多关于fail-fast特性的详细介绍，请参考：[Java 集合系列04之 fail-fast总结(通过ArrayList来说明fail-fast的原理、解决办法)](http://www.cnblogs.com/skywang12345/p/3308762.html)

总结：我们在使用foreach循环时，不要使用add（）和remove（）方法来对集合进行结构上的更改，而是使用迭代器代替进行操作，不然会报并发线程修改异常的错。

```java
// <p>Note that the fail-fast behavior of an iterator cannot be guaranteed
//  as it is, generally speaking, impossible to make any hard guarantees in the
// presence of unsynchronized concurrent modification.  Fail-fast iterators
//  throw <tt>ConcurrentModificationException</tt> on a best-effort basis.
// Therefore, it would be wrong to write a program that depended on this
//  exception for its correctness: <i>the fail-fast behavior of iterators
// should be used only to detect bugs.</i>
 
// 注意,迭代器的fail-fast行为是不能保证的.一般来说,保证非同步的同步操作是不太可能的.在最优基础上,
// Fail-fast迭代器会抛出ConcurrentModificationException.
// 因此,写一个为了自身正确性而依赖于这个异常的程序是不对的.迭代器的fail-fast行为应该只是用来检测bug而
// 已.*/
```

- 要点：这意思就是说，要保证线程安全，还是老老实实使用Collections.synchronizedList（）来包装。

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    private static final long serialVersionUID = 8683452581122892189L;

    /**
     * Default initial capacity.
     * 定义初始化容量为10
     */
    private static final int DEFAULT_CAPACITY = 10;

    /**
       指定该ArrayList容量为0时，返回该空数组。
      Shared empty array instance used for empty instances.
    */

    private static final Object[] EMPTY_ELEMENTDATA = {};

    
    //Shared empty array instance used for default sized empty instances. We
    // distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
    // first element is added.
    // 这个空数组的实例用来给无参构造使用。当调用无参构造方法，返回的是该数组。
    // 刚创建一个ArrayList 时，其内数据量为0。
    // 它与EMPTY_ELEMENTDATA的区别就是：该数组是默认返回的，而后者是在用户指定容量为0时返回。
    
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    
     // The array buffer into which the elements of the ArrayList are stored.
     // The capacity of the ArrayList is the length of this array buffer. Any
     // empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
     // will be expanded to DEFAULT_CAPACITY when the first element is added.
     // 保存添加到ArrayList中的元素。
     // ArrayList的容量就是该数组的长度。
     // 该值为DEFAULTCAPACITY_EMPTY_ELEMENTDATA 时，当第一次添加元素进入ArrayList中时，
     // 数组将扩容至DEFAULT_CAPACITY。
     // 被标记为transient，在对象被序列化的时候不会被序列化
    transient Object[] elementData; // non-private to simplify nested class access

    /**集合的长度
     * The size of the ArrayList (the number of elements it contains).
     *
     * @serial
     */
    private int size;
```

- ArrayList构造方法

```java
  
     // Constructs an empty list with the specified initial capacity
     // @param  initialCapacity  the initial capacity of the list
    //  @throws IllegalArgumentException if the specified initial capacity
    //         is negative
     

// 带初始化值的构造
    public ArrayList(int initialCapacity) {

        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }

    
    // Constructs an empty list with an initial capacity of ten.
     //构造一个初始容量为 10 的空列表。
     
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    
     // Constructs a list containing the elements of the specified
     // collection, in the order they are returned by the collection's
     // iterator.构造一个包含指定 collection 的元素的列表，这些元素是按照该
     // collection 的迭代器返回它们的顺序排列的。
     // @param c the collection whose elements are to be placed into this list
     //@throws NullPointerException if the specified collection is null
     
    public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // c.toArray might (incorrectly) not return Object[] (see 6260652)
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // replace with empty array.
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```

> ArrayList构造一个默认初始容量为10的空列表执行流程： 初始情况：elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {}； size = 0; 2.当向数组中添加第一个元素时，通过add(E e)方法中调用的ensureCapacityInternal(size + 1)方法，即ensureCapacityInternal(1)； 3.在ensureCapacityInternal(int minCapacity)方法中，可得的minCapacity=DEFAULT_CAPACITY=10，然后再调用ensureExplicitCapacity(minCapacity)方法，即ensureExplicitCapacity(10)； 4.在ensureExplicitCapacity(minCapacity)方法中调用grow(minCapacity)方法，即grow(10)，此处为真正具体的数组扩容的算法，在此方法中，通过elementData = Arrays.copyOf(elementData, 10)具体实现了elementData数组初始容量为10的构造。 调整数组的容量： 　　从add()与addAll()方法中可以看出，每当向数组中添加元素时，都要去检查添加元素后的个数是否会超出当前数组的长度，如果超出，数组将会进行扩容，以满足添加数据的需求。数组扩容实质上是通过私有的方法ensureCapacityInternal(int minCapacity) ----> ensureExplicitCapacity(int minCapacity) ------> grow(int minCapacity)来实现的，但在jdk1.8中，向用户提供了一个public的方法ensureCapacity(int minCapacity)使用户可以手动的设置ArrayList实例的容量，以减少递增式再分配的数量。此处与jdk1.6中直接通过一个公开的方法ensureCapacity(int minCapacity)来实现数组容量的调整有区别。

- trimToSize（）方法

说明：将ArrayList的容量设置为当前size的大小。首先需要明确一个概念，ArrayList的size就是ArrayList的元素个数，length是ArrayList申请的内容空间长度。ArrayList每次都会预申请多一点空间，以便添加元素的时候不需要每次都进行扩容操作，例如我们的元素个数是10个，它申请的内存空间必定会大于10，即length>size，而这个方法就是把ArrayList的内存空间设置为size，去除没有用到的null值空间。这也就是我们为什么每次在获取数据长度是都是调用list.size()而不是list.length()。

```java

     // Trims the capacity of this <tt>ArrayList</tt> instance to be the
    // list's current size.  An application can use this operation to minimize
    // the storage of an <tt>ArrayList</tt> instance.
     
    public void trimToSize() {
        modCount++;
        if (size < elementData.length) {
            elementData = (size == 0)
              ? EMPTY_ELEMENTDATA
              : Arrays.copyOf(elementData, size);
        }
    }
```

- 扩容-ensureCapacity等方法

```java
    
     // Increases the capacity of this <tt>ArrayList</tt> instance, if
     // necessary, to ensure that it can hold at least the number of elements
     // specified by the minimum capacity argument.
   
     // @param   minCapacity   the desired minimum capacity  需要的最小容量
     
    public void ensureCapacity(int minCapacity) {

       // 如果elementData等于DEFAULTCAPACITY_EMPTY_ELEMENTDATA，最小扩容量为
       // DEFAULT_CAPACITY，否则为0
        int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            // any size if not default element table
            ? 0
            // larger than default for default empty table. It's already
            // supposed to be at default size.
            : DEFAULT_CAPACITY;
         
         //如果想要的最小容量大于最小扩容量，则使用想要的最小容量。
        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }
 
     
    private static int calculateCapacity(Object[] elementData, int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }

     // 数组容量检查，不够时则进行扩容，只供类内部使用
    private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }

   // 数组容量检查，不够时则进行扩容，只供类内部使用
    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code 确保指定的最小容量 > 数组缓冲区当前的长度 
        if (minCapacity - elementData.length > 0)
         // 扩容
            grow(minCapacity);
    }

    
     // The maximum size of array to allocate.
     // Some VMs reserve some header words in an array.
     // Attempts to allocate larger arrays may result in
     // OutOfMemoryError: Requested array size exceeds VM limit
     //  ===========================================================
     // 因为某些VM会在数组中保留一些头字，尝试分配这个最大存储容量，
     // 可能会导致array容量大于VM的limit，最终导致OutOfMemoryError。
     // ============================================================
     
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

    
     // Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     * ===========================================================
     * 扩容，保证ArrayList至少能存储minCapacity个元素
     * 第一次扩容，逻辑为newCapacity = oldCapacity + (oldCapacity >> 1);
     * 即在原有的容量基础上增加一半。第一次扩容后，如果容量还是小于minCapacity， 
     * 就将容量扩充为minCapacity。
     * ============================================================
     * @param minCapacity the desired minimum capacity
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
        // 获取当前数组容量
        int oldCapacity = elementData.length;
        // 扩容:新的容量=当前容量+当前容量/2.即将当前容量增加一半。 
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        // 如果扩容后的容量还是小于想要的最小容量
        if (newCapacity - minCapacity < 0)
        // 将扩容后的容量再次扩容为想要的最小容量
            newCapacity = minCapacity;
        // 如果扩容后的容量大于临界值，则进行大容量分配
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    // 进行大容量分配
    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```

- 总结：扩容机制为在原来的基础上增加一半

剩余代码主要是一些常规增删改查代码：

![img](/img/2018-7-5-arraylist/v2-e22c19542737cb21f02b8ea9f3d316e5_b.jpg)

其中该集合使用到的设计模式：[迭代器模式](http://www.runoob.com/design-pattern/iterator-pattern.html)

