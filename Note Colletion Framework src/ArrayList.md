# ArrayList
## OverView
### 常用方法分类
根据操作是否改变ArrayList状态，我们可以把ArrayList常用方法分为两类：

* 该变ArrayList状态的方法
	* 增加操作：`add(E e),add(int index,E element),addAll()`
	* 删除操作：`clear(),remove(int index),remove(Object o),removeAll(..)`
	* 修改列表内的元素：`set( ),replaceAll( )`
	* 修改容量： `trimToSize( )`
* 不改变ArrayList状态的方法
	* 查询操作： `contains(Object o),get(int index),isEmpty(),indexOf(),size()
	`	
	* 获取迭代器： `iterator( ), listIterator( )`
	
### 复杂度
#### 常数时间操作：
* `isEmpty() , get(int index), set(int index), size() `
* `listIterator()` 各种操作

#### 线性时间操作
除了上述常数时间操作之外的其他操作，但同样的操作，相对于`LinkedList`的线性时间而言，常数因子要小。

### listIterator
`Collection`接口支持迭代器方法，`List`接口扩展了`Collection`接口，自然也支持迭代器方法，`List`接口使用一个名为`listIterator`的迭代器。`ListIterator`在抽象类`AbstractList`作为一个内部类实现，通过`listIterator()`方法返回。

### 改写计数器
实现`Collection`接口的容器类都支持迭代器方法，因此实现`ArrayList`也支持迭代器方法。为了避免在使用迭代器过程中因`ArrayList`状态改变可能出现的异常。迭代器要有能检测集合状态是否被修改的能力，这种修改变可能来自其他的迭代器或集合自身的，当迭代器检测到了这种修改，便会抛出`ConcurrentModificationException`异常。

为了支持`Iterator`检测机制，`ArrayList`和`Iterator`都分别独立维护一个改写计数器，当`ArrayList`类对象调用可能修改集合状态的方法时，会更新自己的`modCount`改写计数器。同样每次调用`Iterator`具有对集合有着修改能力的方法时，会检查自己的`expectedModCount`与集合的`modCount`是否相等，若不相等，则会抛出异常。


## Instance fields

<pre><code>
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    // instance fields
    private static final int DEFAULT_CAPACITY = 10; // 默认的初始容量

    // 共享空实例，ArrayList类所有empty对象都指向这这两个共享空间。
    private static final Object[] EMPTY_ELEMENTDATA = {};   
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    // 底层fixed-size数组
    transient Object[] elementData; 

    // ArrayList 包含元素的数量
    private int size;
    ...
}

</code></pre>
## Constructors
ArrayList一共支持三种初始化方式。
先来看第一种与第二种，分别是无参构造器和带有初始容量参数的构造器，十分简单。特别地，为了避免创建多个空的对象，这里将所有空的对象指向`DEFAULTCAPACITY_EMPTY_ELEMENTDATA`与`EMPTY_ELEMENTDATA`。
<pre><code>
public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA；
}
</code></pre>
<pre><code>
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
</code></pre>
第三中构造器，接受一个`Collection`初始化。
<pre><code>
public ArrayList(Collection < ? extends E> c) { 
    elementData = c.toArray();  // 修改底层数组引用，使其指向包含c所有元素的一个数组，该数组独立于c
    if ((size = elementData.length) != 0) {
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class)
            // 若elementData不是Object数组，调用一次copyOf方法,以Object类型
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        // 若c为空，仍然指向EMPTY_ELEMENTDATA
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
</code></pre>

## add()方法
ArrayList支持使用两种不同形式的add方法添加加一个元素。
<pre><code>
public void add(int index, E element);	// 在index位置上插入一个元素，无返回值
public boolean add(E e);	// 在ArrayList末尾位置插入一个元素，返回一个布尔值结果
</code></pre>
### public void add(int index, E element)
ArrayList没有insert方法，插入操作由add方法完成，add方法接受一个index参数，实现在index位置上插入一个元素。因为添加一个元素可能会导致capacity不足，因此使用一个私有方法ensureCapacityInternal()方法检查容量并完成动态扩容。当维护capacity操作完成后，通过对底层数组的copy操作将index及之后的元素整体往后挪一个单位，在空出的位置插入element元素。
<pre><code>
public void add(int index, E element) {
    rangeCheckForAdd(index);    // 检查 index 合法性，不合法则会抛出边界异常
    ensureCapacityInternal(size + 1);  // 检查容量、动态扩容
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);		// [index..size-1] 元素整体往后移动一个单位，空出一个位置
    elementData[index] = element;		// 在空出的位置index上插入元素element
    size++;		// 更新元素个数
}
</code></pre>
私有方法`ensureCapacityInternal()`主要做了两个操作

* 更新ArrayList改写计数器`modCount`
* capacity不足时调用`grow()`进行扩容
<pre><code>
private static int calculateCapacity(Object[] elementData, int minCapacity) {
	// 若 elementData 是empty arrayList，返回miniCapacity 与 10 中较大的一个
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}
private void ensureCapacityInternal(int minCapacity) {
	// 传入需要最小Capacity 
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}
//
private void ensureExplicitCapacity(int minCapacity) {
	// 无论是否扩容，都更新改写计数器
    modCount++;
    // 容量不足，进行扩容操作
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
</code></pre>

<pre><code>
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);	// 计划扩大后的容量是原来的Capacity的1.5倍
    if (newCapacity - minCapacity < 0)	//若计划扩大后的容量仍不能满足要求，则将计划容量修改至minCapacity
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0	// 计划容量大于默认的最大量，进行hugeCapcity操作
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);	// 底层数组引用指向Arrays的copyOf()方法的返回值
}
</code></pre>
在grow方法的最后`elementData`指向一个新的容量大小的数组，这个数组是如何得到的呢？为了得到答案我们可以查看`copyOf()`方法的内部发生了什么？
<pre><code>
public static <T> T[] copyOf(T[] original, int newLength) {
    return (T[]) copyOf(original, newLength, original.getClass());
}
public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
    @SuppressWarnings("unchecked")
    // 
    T[] copy = ((Object)newType == (Object)Object[].class)
        ? (T[]) new Object[newLength]
        : (T[]) Array.newInstance(newType.getComponentType(), newLength);
    System.arraycopy(original, 0, copy, 0,
                     Math.min(original.length, newLength));
    return copy;
}
</code></pre>
可以看到`copyOf()`方法实际是开辟了一个新的capacity容量的数组，然后调用`system.arraycopy()`方法将原来的数组中所有元素拷贝到新数组中。

至此我们可以知道向ArrayList添加一个元素最坏的情况：需要扩容操作、并且在在第一个位置插入元素，此时`add(int index, E e)`在线性时间内。

### public boolean add(E e)
<pre><code>
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;    // 将 e 插入ArrayList的末尾，更新 size
    return true;
}
</code></pre>
与上一个形式的`add(int index,E element)`方法类似，均调用了`ensureCapacityInternal()`方法，不同的是插入的位置固定在了最后，类似于C++容器的`push_back()`操作。

## remove()方法
ArrayList

