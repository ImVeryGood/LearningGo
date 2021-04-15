# 一：Java基础 #

  **泛型：** 泛型，即“参数化类型”。一提到参数，最熟悉的就是定义方法时有形参，然后调用此方法时传递实参。那么参数化类型怎么理解呢？顾名思义，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。
  
   **泛型的使用：**泛型有三种使用方式，分别为：泛型类、泛型接口、泛型方法
    

**泛型类实例：**

    //此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参 数常用于表示泛型
    //在实例化泛型类时，必须指定T的具体类型
    public class Generic<T>{ 
    //key这个成员变量的类型为T,T的类型由外部指定  
    private T key;

    public Generic(T key) { //泛型构造方法形参key的类型也为T，T的类型由外部指定
        this.key = key;
    }

    public T getKey(){ //泛型方法getKey的返回值类型为T，T的类型由外部指定
        return key;
    }
    }



      //泛型的类型参数只能是类类型（包括自定义类），不能是简单类型

      //传入的实参类型需与泛型的类型参数类型相同，即为Integer.

      Generic<Integer> genericInteger = new Generic<Integer>(123456);


      //传入的实参类型需与泛型的类型参数类型相同，即为String.

      Generic<String> genericString = new Generic<String>("key_vlaue");

      Log.d("泛型测试","key is " + genericInteger.getKey());

      Log.d("泛型测试","key is " + genericString.getKey());


**注意：**
- 
- 泛型的类型参数只能是类类型，不能是简单类型。
- 不能对确切的泛型类型使用instanceof操作。如下面的操作是非法的，编译时会出错。

**泛型接口：**


    //定义一个泛型接口
    public interface Generator<T> {
       public T next();

      }

**泛型方法：**


     `/**
     * 泛型方法的基本介绍
     * @param tClass 传入的泛型实参
     * @return T 返回值为T类型
     * 说明：
     *     1）public 与 返回值中间<T>非常重要，可以理解为声明此方法为泛型方法。
     *     2）只有声明了<T>的方法才是泛型方法，泛型类中的使用了泛型的成员方法并不是泛型方法。
     *     3）<T>表明该方法将使用泛型类型T，此时才可以在方法中使用泛型类型T。
     *     4）与泛型类的定义一样，此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型。
     */
    public <T> T genericMethod(Class<T> tClass)throws InstantiationException ,
      IllegalAccessException{
        T instance = tClass.newInstance();
        return instance;
     }`

## String,StrindBuilder,StringBuffer区别 ##


**java中对String的具体操作是：**
 
当String中的字符增加时，其实实际上是新开辟了一块存储空间存储改变后的字符串，然后将之前使用的那块空间通过GC进行回收


**String和StringBuilder的区别：**
由上可知：

String每次变化都会开辟新的存储空间，存储数据，然后将之前那块空间通过GC进行回收

StringBuilder是可变长度的，数据的增加是直接在现有空间内进行


**StringBuilder和StringBuffer的区别：**
如果你只是单纯的使用上述两个类，会发现他们的方法几乎相同，使用起来也区别不大

但是如果你在编译器中去查看StringBuffer和StringBuilder的源代码，你会发现StringBuffer的方法中几乎都使用了synchronized关键字，那么现在看来两者的区别就很明显了

StringBuffer    线程安全     多线程的应用程序中使用
StringBuilder   非线程安全  单线程的应用程序中使用


**String、StringBuilder和StringBuffer三者的运行速度比较：**

毫无疑问

StringBuilder-->StringBuffer-->String
String针对的是少量的字符串操作
大量字符串数据进行操作使用StringBuilder和StringBuffer

## Lock和synchronized的区别 ##


1. Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现；

2. synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；
3. Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；
4. 通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。 
5. Lock可以提高多个线程进行读操作的效率。
6. Lock只有代码块锁，synchronized有代码块锁和方法锁
7. Lock可以提高多个线程进行读操作的效率。

在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时Lock的性能要远远优于synchronized。所以说，在具体使用时要根据适当情况选择。


**synchronized是java中的一个关键字，也就是说是Java语言内置的特性。那么为什么会出现Lock呢？**
我们了解到如果一个代码块被synchronized修饰了，当一个线程获取了对应的锁，并执行该代码块时，其他线程便只能一直等待，等待获取锁的线程释放锁，而这里获取锁的线程释放锁只会有两种情况：

1.获取锁的线程执行完了该代码块，然后线程释放对锁的占有；

2.线程执行发生异常，此时JVM会让线程自动释放锁。

　　那么如果这个获取锁的线程由于要等待IO或者其他原因（比如调用sleep方法）被阻塞了，但是又没有释放锁，其他线程便只能干巴巴地等待，试想一下，这多么影响程序执行效率。

因此就需要有一种机制可以不让等待的线程一直无期限地等待下去（比如只等待一定的时间或者能够响应中断），通过Lock就可以办到。
　　再举个例子：当有多个线程读写文件时，读操作和写操作会发生冲突现象，写操作和写操作会发生冲突现象，但是读操作和读操作不会发生冲突现象。

**但是采用synchronized关键字来实现同步的话，就会导致一个问题：**

如果多个线程都只是进行读操作，所以当一个线程在进行读操作时，其他线程只能等待无法进行读操作。

因此就需要一种机制来使得多个线程都只是进行读操作时，线程之间不会发生冲突，通过Lock就可以办到。

另外，通过Lock可以知道线程有没有成功获取到锁。这个是synchronized无法办到的。

总结一下，也就是说Lock提供了比synchronized更多的功能。但是要注意以下几点：

1）Lock不是Java语言内置的，synchronized是Java语言的关键字，因此是内置特性。Lock是一个类，通过这个类可以实现同步访问；

2）Lock和synchronized有一点非常大的不同，采用synchronized不需要用户去手动释放锁，当synchronized方法或者synchronized代码块执行完之后，系统会自动让线程释放对锁的占用；而Lock则必须要用户去手动释放锁，如果没有主动释放锁，就有可能导致出现死锁现象。


**Lock锁的用法**

1、使用ReentrantLock实现同步

lock()方法:上锁

unlock()方法：释放锁

     public void run() {
           while (flag){
            lock.lock();//加锁
            try {
                try {
                    buy();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            } finally {
                lock.unlock();//解锁
            }
 
        }

## ArrayList和LinkedList区别及使用场景 ##

LinkedList和ArrayList的差别主要来自于Array和LinkedList数据结构的不同。

1. ArrayList是基于数组实现的，LinkedList是基于双链表实现的。
另外LinkedList类不仅是List接口的实现类，可以根据索引来随机访问集合中的元素，
除此之外，LinkedList还实现了Deque接口，Deque接口是Queue接口的子接口，它代表一个双向队列，因此LinkedList可以作为双向队列 ，栈（可以参见Deque提供的接口方法）和List集合使用，功能强大。

2. 因为Array是基于索引(index)的数据结构，它使用索引在数组中搜索和读取数据是很快的，可以直接返回数组中index位置的元素，因此在随机访问集合元素上有较好的性能。Array获取数据的时间复杂度是O(1),但是要插入、删除数据却是开销很大的，因为这需要移动数组中插入位置之后的的所有元素。

3. 相对于ArrayList，LinkedList的随机访问集合元素时性能较差，因为需要在双向列表中找到要index的位置，再返回；但在插入，删除操作是更快的。因为LinkedList不像ArrayList一样，不需要改变数组的大小，也不需要在数组装满的时候要将所有的数据重新装入一个新的数组，这是ArrayList最坏的一种情况，时间复杂度是O(n)，而LinkedList中插入或删除的时间复杂度仅为O(1)。ArrayList在插入数据时还需要更新索引（除了插入数组的尾部）。

4. LinkedList需要更多的内存，因为ArrayList的每个索引的位置是实际的数据，而LinkedList中的每个节点中存储的是实际的数据和前后节点的位置。

**使用场景：**

1. 如果应用程序对数据有较多的随机访问，ArrayList对象要优于LinkedList对象；

2. 如果应用程序有更多的插入或者删除操作，较少的随机访问，LinkedList对象要优于ArrayList对象；
 
3. 不过ArrayList的插入，删除操作也不一定比LinkedList慢，如果在List靠近末尾的地方插入，那么ArrayList只需要移动较少的数据，而LinkedList则需要一直查找到列表尾部，反而耗费较多时间，这时ArrayList就比LinkedList要快。

**HashMap 和 HashTable 区别**

**HashMap 不是线程安全的**

HashMap 是 map 接口的实现类，是将键映射到值的对象，其中键和值都是对象，并且不能包含重复键，但可以包含重复值。HashMap 允许 null key 和 null value，而 HashTable 不允许。

**HashTable 是线程安全 Collection。**

HashMap 是 HashTable 的轻量级实现，他们都完成了Map 接口，主要区别在于 HashMap 允许 null key 和 null value,由于非线程安全，效率上可能高于 Hashtable。

**区别如下：**

- HashMap允许将 null 作为一个 entry 的 key 或者 value，而 Hashtable 不允许。
- HashMap 把 Hashtable 的 contains 方法去掉了，改成 containsValue 和 containsKey。因为 contains 方法容易让人引起误解。
- HashTable 继承自 Dictionary 类，而 HashMap 是 Java1.2 引进的 Map interface 的一个实现。
- HashTable 的方法是 Synchronize 的，而 HashMap 不是，在多个线程访问 Hashtable 时，不需要自己为它的方法实现同步，而 HashMap 就必须为之提供外同步。
- Hashtable 和 HashMap 采用的 hash/rehash 算法都大概一样，所以性能不会有很大的差异。







# Android #


**drawable设置阴影**

    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item>
        <shape android:shape="rectangle">
            <corners android:radius="@dimen/y30" />
            <gradient
                android:angle="270"
                android:endColor="@color/color_3f8473"
                android:startColor="@color/colorPrimary"
                android:centerX="0.5"
                android:centerY="0.5"
                android:type="linear" />
        </shape>
    </item>
    <item
        android:bottom="2dp"
        android:left="4dp"
        android:top="@dimen/x9">
        <shape android:shape="rectangle">
            <solid android:color="@color/Red" />
            <corners android:radius="@dimen/y30" />
        </shape>
    </item>

    </layer-list>


**设置文本阴影**
  
**字体阴影需要四个相关参数：**

1. android:shadowColor：阴影的颜色
2. android:shadowDx：水平方向上的偏移量
3. android:shadowDy：垂直方向上的偏移量
4. android:shadowRadius：是阴影的的半径大少 0 是没有，值越大阴影越大越模糊

**dx  dy 分别为   （0 ， 0） ， （10 ， 10 ） ， （30 ， 30）**

结果如下：
![](https://img-blog.csdn.net/20141011142658278?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGV3ZW5jZTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**dx  dy 都是 30  shadowRadius  分别为： 0 ， 0.01 ， 1 ， 2 ， 5 ， 10**

结果：
![](https://img-blog.csdn.net/20141011143555964?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGV3ZW5jZTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

调试dx  跟 dy来改变光源，使阴影偏向不同的方向 跟  距离

 如果光源是在左边，那么dx 是为正的， 

光源在最右边，那么dx就是负

光源在上  那么dy 为 正

光源在下， 那么dy 为 负  

**shadowRadius 都是 1  （5,0） ; （-5,0） ;（0,5） ;（0，-5） ;**

如下：

![](https://img-blog.csdn.net/20141011145513484?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGV3ZW5jZTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

带一点浮雕效果：把dx  dy都设置较小的值
设置为 （0.2 ， 0.2） ， （1 ， 1） ， （2 ， 2）

![](https://img-blog.csdn.net/20141011150421656?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGV3ZW5jZTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

荧光灯的效果： 把把dx  dy设置为0 ， Raduis位置较大就行了，最重要的事字体颜色 跟背景颜色一样（或者非常相近）