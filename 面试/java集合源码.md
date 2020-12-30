# Arraylist

```
存放元素：有序
线程不安全
扩容和索容
基于数组
```

## 初始化以及add()方法

```
 private static final int DEFAULT_CAPACITY = 10;  //默认初始容量为10
 
 
  public boolean add(E e) {
        //第一次进来初始化，后面是判断是否需要扩容的操作
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
    
   private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }
    
    
    private static int calculateCapacity(Object[] elementData, int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
             //刚开始数组为空，就初始化容量为10
          return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        //如果不为空就返回 size+1这个数值
        return minCapacity;
    }
    
     private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code (如果size为10，传入size+1,11此时大于10 ，所以需要扩容)
        //第一次进来的时候，10-0> 会进行扩容
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
    
    
    
    //扩容
      private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        //扩容1.5倍
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        //第一次 add的时候，将初始容量设为10
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        
        //第一次进来相当于数组的初始化，后面是扩容操作
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
    
```

## remove()方法

![image-20201110210714129](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201110210714129.png)

### 基于index删除

```
 public E remove(int index) {
     //检查是否超出数组的容量
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work

        return oldValue;
    }
    
    private void rangeCheck(int index) {
        if (index >= size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
```



### 基于value删除

```
public boolean remove(Object o) {
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }
    
    
    private void fastRemove(int index) {
        modCount++;
        
        int numMoved = size - index - 1; //index后面剩余的元素个数
        
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
         //方便垃圾回收
        elementData[--size] = null; // clear to let GC do its work
    }
```

## modCount的作用

可以参考我的博客 

https://blog.csdn.net/cbxazm/article/details/109114459

```
在多线程的情况下，保证安全性
fast-fail机制 
每次remove或者add的时候都会modCount++ ，当modCount!=expectCount时，就会发生错误
```

## ArrayList跟Vector的区别？

```
都是基于数组的，但是Vector加了synchronized锁，是线程安全的 

ArrayList扩容1.5倍

Vector扩容2倍

capacityIncrement 这个是可以自定义的扩容容量，没有传的话，就是扩容两倍

int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
```

# LinkedList

```
基于双向链表
```

## Node节点

```
private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

## add()方法

```
 public boolean add(E e) {
        linkLast(e);
        return true;
    }
   
   
   /**
     * Links e as last element.
     */
    void linkLast(E e) {
        final Node<E> l = last;
        //新节点追加到尾结点的后面
        final Node<E> newNode = new Node<>(l, e, null);
        //尾插法
        last = newNode;
        if (l == null)
          //刚开始头尾都是空
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }
    
```

## get()方法

也是基于索引值来操作的

```
 public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
    
   //相当于二分查找
Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) {
        //小于size/2 的就从first开始遍历查询
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
       } else {
       //大于size/2的就从last往前开始遍历
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
```

## remove()方法

```
 public E remove(int index) {
        checkElementIndex(index);
        return unlink(node(index));
    }
```

```
 E unlink(Node<E> x) {
        // assert x != null;
        final E element = x.item;
        final Node<E> next = x.next;
        final Node<E> prev = x.prev;

        if (prev == null) {
           //如果是头结点，那就设置first到下一个节点
            first = next;
        } else {
            prev.next = next;
            x.prev = null;  //Help Gc
        }

        if (next == null) {
        //如果删除的是最后一个节点，那就把last指向prev节点
            last = prev;
        } else {
            next.prev = prev;
            x.next = null; //Help GC
        }

        x.item = null;
        size--;
        modCount++;
        return element;
    }
```

# 1.7 HashMap

## 构造方法

```
public HashMap() {
    //  16 0.75f
        this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR);
    }
 
    public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);

        this.loadFactor = loadFactor;
        threshold = initialCapacity; //初始化threshold 为默认容量16
        init();  //子类LinkedHashMap 实现的init()方法
    }
```



## 

## 数组容量只取2的幂次方

```
int i = indexFor(hash, table.length);
        //h & (length-1)  // 长度16 -1 15 0000 1111 低4位是1，任何数&上高4位都是0 ，所以数组下标取
        值范围在0000 - 1111 之间，只需要看他的低位 
        (所以HashMap中数组的容量必须是2的幂次方，便于-1后低位全是1，能控制数组下标的范围)
```

## hash方法的作用

**提升散列性**

**尽量key的hashCode的高28位也参与进来，不然前28位都是无效的，因为&上15只需要看它的最后四位，这样的话前28位没有作用，发生冲突的可能性太大了，降低了get方法的效率**

```
 final int hash(Object k) {
        int h = hashSeed;
        if (0 != h && k instanceof String) {
            return sun.misc.Hashing.stringHash32((String) k);
        }

        h ^= k.hashCode();

        // This function ensures that hashCodes that differ only by
        // constant multiples at each bit position have a bounded
        // number of collisions (approximately 8 at default load factor).
        h ^= (h >>> 20) ^ (h >>> 12);
        return h ^ (h >>> 7) ^ (h >>> 4);
    }
    
```

## put()方法

```
 public V put(K key, V value) {
        if (table == EMPTY_TABLE) {
            inflateTable(threshold);
        }
        if (key == null)
            return putForNullKey(value);
            //可存入 一个 Null 键为key 的值
        int hash = hash(key);
        int i = indexFor(hash, table.length);
        //h & (length-1)  // 长度16 -1 15 0000 1111 低4位是1，任何数&上高4位都是0 ，所以数组下标取
        值范围在0000 - 1111 之间，只需要看他的低位 
        (所以HashMap中数组的容量必须是2的幂次方，便于-1后低位全是1，能控制数组下标的范围)
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            //把hash比较放在前面是因为，如果equals相等，那么hash一定是相等的，反之不然(先执行equals可能比较慢)
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
            
                //hash值相等 ，并且key equals为true就直接覆盖 该key的value
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }

        modCount++;
        //正常添加元素
        addEntry(hash, key, value, i);
        return null;
    }
```

```
 /**
     * Inflates the table.
     */
    private void inflateTable(int toSize) {
        // Find a power of 2 >= toSize
        //找到大于等于toSize的2的幂次方数
        int capacity = roundUpToPowerOf2(toSize);  //传入16 ，找到的还是16

        //重新计算阈值为 capacity * 加载因子 0.75    ==12 (用于扩容的阈值)
        threshold = (int) Math.min(capacity * loadFactor, MAXIMUM_CAPACITY + 1);
        
        //初始化数组
        table = new Entry[capacity];
        
        initHashSeedAsNeeded(capacity);     //初始化hashSeed
    }
```

```
private static int roundUpToPowerOf2(int number) {
    // assert number >= 0 : "number must be non-negative";
    return number >= MAXIMUM_CAPACITY
            ? MAXIMUM_CAPACITY
            // 这里传入的是(number-1)*2 ，然后找到的就是大于等于number的2的幂次方数
            : (number > 1) ? Integer.highestOneBit((number - 1) << 1) : 1;
}
```

```

public static int highestOneBit(int i) {
        // HD, Figure 3-1
        
         //将一个数的高位1 后面全值置为1
        i |= (i >>  1);
        i |= (i >>  2);
        i |= (i >>  4);
        i |= (i >>  8);
        i |= (i >> 16);
        
        // 比如一个数是10 上面五个步骤的结果就是 i=0000 1111 
        
        0000 1111 - 0000 0111 只剩下最高位的一个1 ,所以结果就是8 就是小于等于10的最大的2的幂次方数
        return i - (i >>> 1);
    }
```

## addEntry()方法

```
 void addEntry(int hash, K key, V value, int bucketIndex) {
   //当size大于等于阈值，并且当前数组位置上并没有节点是，我们进行扩容操作
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);   /、扩容两倍
            hash = (null != key) ? hash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }

        createEntry(hash, key, value, bucketIndex);
    }
```

## createEntry()方法

```
void createEntry(int hash, K key, V value, int bucketIndex) {
        Entry<K,V> e = table[bucketIndex];
        table[bucketIndex] = new Entry<>(hash, key, value, e); //就是将新元素放在了头部
        size++;
    }
```

## resize()方法(1.7中先扩容，再添加元素，1.8中相反)

   **扩容前是16 扩容后是32   16--> &15  32-->&31   所以扩容后对应的数组下标位置要么不变，要么就是原位置加上以前的size 16的位置**
    0000 1111
    0001 1111 最高位1的位置，取或者不取，取的话扩容后位置就加16

```
void resize(int newCapacity) {
        Entry[] oldTable = table;
        int oldCapacity = oldTable.length;
        
        if (oldCapacity == MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return;
        }

        Entry[] newTable = new Entry[newCapacity];
        transfer(newTable, initHashSeedAsNeeded(newCapacity));
        table = newTable;
        threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
    }
```

## transfer()方法

**扩容后同一节点上元素如果还放在同一个节点 顺序会相反**

``` 
void transfer(Entry[] newTable, boolean rehash) {
        int newCapacity = newTable.length;
        for (Entry<K,V> e : table) {
            while(null != e) {
                Entry<K,V> next = e.next;
                if (rehash) {
                    e.hash = null == e.key ? 0 : hash(e.key);
                }
                int i = indexFor(e.hash, newCapacity);
                e.next = newTable[i];
                newTable[i] = e;
                e = next;
            }
        }
    }
    
   
```

# 1.7HashMap多线程环境会出现死链状况

假设现在线程1和线程2同时执行transfer方法 ，线程2 先阻塞住 ，待线程1执行完才进行操作

![image-20201118162844028](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201118162844028.png)



![image-20201118163011926](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201118163011926.png)

**第一次循环**

![image-20201118163305804](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201118163305804.png)

![image-20201118163340567](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201118163340567.png)

**第二次循环**
![image-20201118163504518](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201118163504518.png)



![image-20201118163611222](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201118163611222.png)

![image-20201118163641475](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201118163641475.png)

![image-20201118163713638](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201118163713638.png)

**第三次循环**

![image-20201118163758110](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201118163758110.png)



![image-20201118163850323](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201118163850323.png)

![image-20201118163954576](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201118163954576.png)

# 1.7ConcurrentHashMap

## 构造方法

```
 public ConcurrentHashMap() {
        //第一个hashEnrtry数组的大小
        
        //第三个指的要Segment数组大小，必须大于等于它
        
        this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR, DEFAULT_CONCURRENCY_LEVEL);
    }
```



```
 public ConcurrentHashMap(int initialCapacity,
                             float loadFactor, int concurrencyLevel) {
        if (!(loadFactor > 0) || initialCapacity < 0 || concurrencyLevel <= 0)
            throw new IllegalArgumentException();
        if (concurrencyLevel > MAX_SEGMENTS)
            concurrencyLevel = MAX_SEGMENTS;
        // Find power-of-two sizes best matching arguments
        int sshift = 0;
        int ssize = 1;
        while (ssize < concurrencyLevel) {
           //找到大于等于并发级别的2的幂次方数
            ++sshift;
            ssize <<= 1;
        }
        this.segmentShift = 32 - sshift;
        this.segmentMask = ssize - 1;
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        int c = initialCapacity / ssize;
        
        if (c * ssize < initialCapacity)
             //如果是33/16 ==》 c=2
             //2*16《33 所以需要加1
            ++c;
        int cap = MIN_SEGMENT_TABLE_CAPACITY; //默认为2
        while (cap < c)
            //HashEntry的容量也必须是2的幂次方数
            cap <<= 1;
        // create segments and segments[0]
        Segment<K,V> s0 =
        
        //cap * loadFactor 是下面数组扩容的阈值
            new Segment<K,V>(loadFactor, (int)(cap * loadFactor),
                             (HashEntry<K,V>[])new HashEntry[cap]);
                             
         //创建一个ssize大小的Segment数组
        Segment<K,V>[] ss = (Segment<K,V>[])new Segment[ssize];
        //先把s0放到Segment数组的第0个位置
        UNSAFE.putOrderedObject(ss, SBASE, s0); // ordered write of segments[0]
        this.segments = ss;
    }
```

## put()方法(key ,value 都不能为null)

```
  @SuppressWarnings("unchecked")
    public V put(K key, V value) {
        Segment<K,V> s;
        if (value == null)  //value不可以为NULL
            throw new NullPointerException();
        int hash = hash(key);   //key也不可以为NULL，hashMap中这部操作之前进行了操作，如果key==null，进入这个方法会报错。
        
         //segmentMask 构造方法里设置的就是ssize-1 ,就是segment数组的长度-1
         
         //右移 segmentShift(构造函数里有赋值) ，这里算segment数组下标，用hashcode高位参与运算
        int j = (hash >>> segmentShift) & segmentMask; 
        
        //UNSAFE.getObject(segments, (j << SSHIFT) + SBASE)) 就是获取segment数组中第j个位置的 
        
      //SSHIFT = 31 - Integer.numberOfLeadingZeros(ss);
      
        if ((s = (Segment<K,V>)UNSAFE.getObject          // nonvolatile; recheck
             (segments, (j << SSHIFT) + SBASE)) == null) //  in ensureSegment
             
            s = ensureSegment(j); //初始化并返回Segment数组的下标位置
        return s.put(key, hash, value, false);
    }
```

## numberOfLeadingZeros（）方法

**算出这个数最高位1前 有多少个0**

```
 public static int numberOfLeadingZeros(int i) {
        // HD, Figure 5-6
        if (i == 0)
            return 32;
        int n = 1;
        if (i >>> 16 == 0) { n += 16; i <<= 16; }
        if (i >>> 24 == 0) { n +=  8; i <<=  8; }
        if (i >>> 28 == 0) { n +=  4; i <<=  4; }
        if (i >>> 30 == 0) { n +=  2; i <<=  2; }
        n -= i >>> 31;
        return n;
    }
```



## ensureSegment()方法

```
    @SuppressWarnings("unchecked")
    private Segment<K,V> ensureSegment(int k) {
        final Segment<K,V>[] ss = this.segments;
        long u = (k << SSHIFT) + SBASE; // raw offset
        Segment<K,V> seg;
        
        if ((seg = (Segment<K,V>)UNSAFE.getObjectVolatile(ss, u)) == null) {
            Segment<K,V> proto = ss[0]; // use segment 0 as prototype
            int cap = proto.table.length;  //使用之前segment[0]位置的元素作模板把
            float lf = proto.loadFactor;
            int threshold = (int)(cap * lf);
            HashEntry<K,V>[] tab = (HashEntry<K,V>[])new HashEntry[cap];
            if ((seg = (Segment<K,V>)UNSAFE.getObjectVolatile(ss, u))
                == null) { // recheck
                Segment<K,V> s = new Segment<K,V>(lf, threshold, tab);
                while ((seg = (Segment<K,V>)UNSAFE.getObjectVolatile(ss, u))
                       == null) {
                       //在第u个位置进行赋值操作
                    if (UNSAFE.compareAndSwapObject(ss, u, null, seg = s))
                        break;
                }
            }
        }
        return seg;
    }
```

## Segment的put()方法

```
 final V put(K key, int hash, V value, boolean onlyIfAbsent) {
            HashEntry<K,V> node = tryLock() ? null :
                scanAndLockForPut(key, hash, value);
                //加锁 ，并新建node
            V oldValue;
            try {
                HashEntry<K,V>[] tab = table;
                int index = (tab.length - 1) & hash;
                //cas 取 HashRntry数组的对应位置值
                HashEntry<K,V> first = entryAt(tab, index);
                for (HashEntry<K,V> e = first;;) {
                    //e != null 遍历链表 ，进行key value相等情况下的覆盖操作
                    if (e != null) {
                        K k;
                        if ((k = e.key) == key ||
                            (e.hash == hash && key.equals(k))) {
                            oldValue = e.value;
                            if (!onlyIfAbsent) {
                                e.value = value;
                                ++modCount;
                            }
                            break;
                        }
                        e = e.next;
                    }
                    else {
                        if (node != null)
                            node.setNext(first);
                        else
                               //使用头插法 进行操作
                            node = new HashEntry<K,V>(hash, key, value, first);
                        int c = count + 1;
                        //先进行扩容，再放入节点
                        if (c > threshold && tab.length < MAXIMUM_CAPACITY)
                            rehash(node);
                        else
                            setEntryAt(tab, index, node);  //相当于放到头的位置
                        ++modCount;
                        count = c;
                        oldValue = null;
                        break;
                    }
                }
            } finally {
                unlock();
            }
            return oldValue;
        }
```

## rehash()方法

**只会对当前Segment数组位置的HashEtry数组进行扩容**

```
   @SuppressWarnings("unchecked")
        private void rehash(HashEntry<K,V> node) {
            HashEntry<K,V>[] oldTable = table;
            int oldCapacity = oldTable.length;
            int newCapacity = oldCapacity << 1;  //扩容两倍
            threshold = (int)(newCapacity * loadFactor);
            HashEntry<K,V>[] newTable =
                (HashEntry<K,V>[]) new HashEntry[newCapacity];
            int sizeMask = newCapacity - 1;  //方便控制数组的下标
            for (int i = 0; i < oldCapacity ; i++) {
                HashEntry<K,V> e = oldTable[i];
                if (e != null) {
                    HashEntry<K,V> next = e.next;
                    int idx = e.hash & sizeMask;  //计算新数组的下标
                    if (next == null)   //  Single node on list
                        newTable[idx] = e;   //老数组位置只有一个元素，直接放到新数组位置
                    else { // Reuse consecutive sequence at same slot
                        HashEntry<K,V> lastRun = e;
                        int lastIdx = idx;
                        for (HashEntry<K,V> last = next;
                             last != null;
                             last = last.next) {
                            int k = last.hash & sizeMask;
                             //只会记录最后相等的几个元素下标
                            if (k != lastIdx) {
                              如果是相等的什么也不操作 ，不相等就 更新 值
                                lastIdx = k;
                                lastRun = last;
                            }
                        }
                        newTable[lastIdx] = lastRun; //把最后几个放在新数组同一位置的 进行转移
                        // Clone remaining nodes
                        for (HashEntry<K,V> p = e; p != lastRun; p = p.next) {
                           // 移动到lastRun上面的元素
                            V v = p.value;
                            int h = p.hash;
                            int k = h & sizeMask;
                            //头插 转移
                            HashEntry<K,V> n = newTable[k];
                            newTable[k] = new HashEntry<K,V>(h, p.key, v, n);
                        }
                    }
                }
            }
            int nodeIndex = node.hash & sizeMask; // 放入新节点
            node.setNext(newTable[nodeIndex]);
            newTable[nodeIndex] = node;
            table = newTable;
        }
```



# 1.8HashMap

## 属性

```
static final int TREEIFY_THRESHOLD = 8;

static final int UNTREEIFY_THRESHOLD = 6;

达到8 就转换为红黑树，减少到6就转换为链表，中间空一个是因为，怕频繁的一个插入一个删除，就会来回切换树根链表，太影响效率了。
```

## hash()方法

```
 static final int hash(Object key) {
     //右移和异或运算，尽量让高位也参与进来，防止一大丢数的数组下标位置一样
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```

## put()方法

```
 public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
    
    
 final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            //如果table为空，就先去初始化一下
            n = (tab = resize()).length;
            
        if ((p = tab[i = (n - 1) & hash]) == null)
            //放的位置为空，直接放入数组中
           tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                 //遍历链表，找到key相等的
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        //如果当前节点数大于8 ,那么就转换为红黑树
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            
            //对于key相等的进行覆盖操作
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        
        ++modCount;
        if (++size > threshold)
         //扩容操作
            resize();
        afterNodeInsertion(evict);
        return null;
    }

```

## resize()方法(初始化和扩容都在里面)

```
 final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```

## treeifyBin()方法

```
final void treeifyBin(Node<K,V>[] tab, int hash) {
        int n, index; Node<K,V> e;
        if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
           //如果 整个总的节点数小于64，也不会进行树化，会进行扩容
            resize();
        else if ((e = tab[index = (n - 1) & hash]) != null) {
            TreeNode<K,V> hd = null, tl = null;
            do {
                TreeNode<K,V> p = replacementTreeNode(e, null);
                if (tl == null)
                    hd = p;
                else {
                    p.prev = tl;
                    tl.next = p;
                }
                tl = p;
            } while ((e = e.next) != null);
            if ((tab[index] = hd) != null)
                hd.treeify(tab);
        }
    }
```

