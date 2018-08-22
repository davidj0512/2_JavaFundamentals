### 遗漏点：

> * 内部类(CoreJava1-P242)
> * 代理（CoreJava1-P258）
> * 日志、调试技巧（CoreJava1-P289）
> * 泛型语法，规则等（参考别的资料吧，觉得CoreJava讲的有点烂）
> * sort,排序算法，看`Collections.sort, Arrays.sort`源码，自己写几种排序算法。
> * TreeSet（树集）是有序集合，他的排序是使用树结构完成的，当前使用的是**红黑树（red-black tree）**。学习和写红黑树

### 1. ArrayList中ensureCapacity的使用与优化:

如果已经预知容器可能会装多少元素，最好显示的调用ensureCapacity这个方法一次性扩容到位。如果没有一次性扩到想要的最大容量的话，它就会在添加元素的过程中，一点一点的进行扩容，要知道对数组扩容是要进行数组拷贝的，这就会浪费大量的时间。 

```java
final int N=1000000;
ArrayList list2=new ArrayList();
list2.ensureCapacity(N);//显示的对低层数组进行扩容
for(int i=0;i<N;i++){
	list2.add(obj);
}
System.out.println(System.currentTimeMillis()-start2);
```

Ref: [ArrayList中ensureCapacity的使用与优化](https://blog.csdn.net/nzh1234/article/details/22752095)

一旦能够确定数组列表（ArrayList）的大小不再发生变化，就可以调用**trimToSize**方法来移除掉不用的存储空间，以节省空间。但若是再添加新元素就需要花时间对数组列表扩容。所以应该在确认不会添加任何元素时，再调用trimToSize方法。

### 2. 断言，assert

我觉得可以在调试程序，懒得用IDE的Debug工具时，简单使用一下assert，用assert判断一下变量值的正确性等。其他没啥用吧。
（注意，默认断言被禁用，要设置VM参数-ea或-enableassertions来启用断言）

Ref: [Java中的断言assert](https://www.cnblogs.com/zoraliu66/p/6537066.html)

### 3. 使用新特性：

* java7，使用带资源的try语句（try-with-resources），当资源（例如文件）不再需要时能够自行释放
* java7，多重捕获异常(multi-catch)，本来不同的异常是由多个catch语句块来处理，现在可以把多个异常用一个catch语句块来处理，用’|’来表示多个异常，也就是或的意思（但多个异常间不能是父子类的关系）
* java7，Throwable类添加addSuppressed方法和getSuppressed方法。支持原始异常中加入被抑制的异常。 异常抑制：在try和finally中同一时候抛出异常时，finally中抛出的异常会在异常栈中向上传递，而try中产生的原始异常会消失。在Java7之前的版本，要将原始异常保存，再在finally中产生异常时抛出原始异常； 在Java7的版本中，能够使用addSuppressed方法记录被抑制的异常。
在Java7之前的版本号，能够将原始异常保存。在finally中产生异常时抛出原始异常：
* java7，泛型实例化类型自动推断，（即<>运算符）`List<String> list = new ArrayList<>();`
* java7，数值类型字面值可以用多个"_"分隔增加可读性, `int a = 123_456;  double b = 123_456e3;`
* java7，增加二进制表示，Java7前支持十进制（123）、八进制（0123）、十六进制（0X12AB）；Java7增加二进制表示（0B11110001、0b11110001）；	`int binary = 0b0001_1001;`
* java7，可以使用字符串控制switch语句。
* java7，对Collections的优化： 原来：
>```java
>List<String> list = new ArrayList<String>();       
>list.add("item");       
>String item = list.get(0);       
>        
>Set<String> set = new HashSet<String>();       
>set.add("item"); 
> 
>Map<String, Integer> map = new HashMap<String, Integer>();       
>map.put("key", 1);       
>int value = map.get("key");     
>```
>
>现在可以这样写：(测试不对呀。。。？)
>
>```java
>List<String> list = ["item"];       
>String item = list[0];       
>           
>Set<String> set = {"item"};       
>            
>Map<String, Integer> map = {"key" : 1};       
>int value = map["key"];     
>```

* java8中调用**Iterator.forEachRemaining**方法并提供一个lambda表达式就可以遍历并处理Collection中的每一个元素，都不用写循环了。(也可用List.forEach() or Map.forEach(), forEach是Iterable接口中的方法)
> ```java
> 	ArrayList<String> list = new ArrayList<>();
> 	list.add("1st");
> 	list.add("2nd");
> 	list.add("3rd");
> 	list.iterator().forEachRemaining(System.out::println);
> ```

### 4. Java8之方法引用

方法引用是用来直接访问类或者实例的已经存在的方法或者构造方法。方法引用提供了一种引用而不执行方法的方式，它需要由兼容的函数式接口构成的目标类型上下文。计算时，方法引用会创建函数式接口的一个实例。

当Lambda表达式中只是执行一个方法调用时，不用Lambda表达式，直接通过方法引用的形式可读性更高一些。方法引用是一种更简洁易懂的Lambda表达式。

注意方法引用是一个Lambda表达式，其中方法引用的操作符是双冒号"::"。

Ref: [Java8之方法引用](https://www.cnblogs.com/xiaoxi/p/7099667.html)

（我总结：可直接用作方法引用的方法，前提是它的函数签名要和函数式接口中的函数描述符一致，即参数列表和返回值同函数式接口的一致即可。）

### 5. 要尽可能使用Iterator接口中的remove方法而不是用Collection接口中的remove方法

Iterator接口中包含一个方法，叫做remove()。该方法可以删除next最新返回的项。虽然Collection接口也包含一个remove方法，但是，使用Iterator的remove可能有更多的优点。
当我们在遍历集合时，如果这当中出现改变集合结构的操作（add、remove或clear方法），会抛出ConcurrentModificationException异常。
当我们获取这个集合的迭代器然后用迭代器进行遍历时，只有迭代器用了他自己的remove方法时，才不会报异常，如果你执行了其他可能改变集合结构的操作它还是会抛异常。因为只有Iterator接口的方法是安全的。

Ref: [为什么我们要尽可能使用Iterator接口中的remove方法而不是用Collection接口中的remove方法](https://blog.csdn.net/u013096088/article/details/51764918)
[Iterator的remove()和Collection的remove()](https://blog.csdn.net/qh_java/article/details/50154405)

### 6. ListIterator（CoreJava1-P356）

LinkedList.add 方法将对象添加到链表的尾部。但是，常常需要将元素添加到链表的中间。由于迭代器是描述集合中位置的，所以这种**依赖于位置的add方法将由迭代器负责**。只有**对自然有序的集合使用迭代器添加元素才有实际意义**。例如，下一节将要讨论的集（ set ) 类型，其中的元素完全无序。因此， 在Iterator 接口中就没有add 方法。相反地，集合类库提供了子接口**Listlterator**, 其中包含add 方法。Listlterator 接口还有两个方法：`E previous();boolean hasPrevious()` 可以用来反向遍历链表。还有一个`set`方法用一个新元素取代调用`next`或`previous`方法返回的上一个元素。还有`nextIndex(); previousIndex()`两个方法，他们的效率非常高，因为列表迭代器保持着当前位置的计数值。

可以想象， 如果在某个迭代器修改集合时， 另一个迭代器对其进行遍历， 一定会出现混乱的状况。例如，一个迭代器指向另一个迭代器刚刚删除的元素前面， 现在这个迭代器就是无效的，并且不应该再使用。**链表迭代器的设计使它能够检测到这种修改**。如果迭代器发现它的集合被另一个迭代器修改了， 或是被该集合自身的方法修改了， 就会抛出一个ConcurrentModificationException 异常。例如， 看一看下面这段代码：
```java
List<String> list = . .
ListIterator<String> iterl = list.listlteratorO ;
ListIterator<String> iter2 = list.listlteratorO ;
iterl.nextO;
iterl.remove0；
iter2.next () ; // throws ConcurrentModificationException
```
对于并发修改列表的检测肴一个奇怪的例外。链表只负责跟踪对列表的结构性修改， 例如， 添加元素、删除元素。**set 方法不被视为结构性修改**。可以将多个迭代器附加给一个链表， 所有的迭代器都调用set 方法对现有结点的内容进行修改。在本章后面所介绍的Collections 类的许多算法都需要使用这个功能。

为了避免发生并发修改的异常，请遵循下述简单规则：**可以根据需要给容器附加许多的迭代器，但是这些迭代器只能读取列表。另外，再单独附加一个既能读又能写的迭代器**。

**原理：**有一种简单的方法可以检测到并发修改的问题。集合可以跟踪改写操作（诸如添加或删除元素）的次数。每个迭代器都维护一个独立的计数值。在每个迭代器方法的开始处检查自己改写操作的计数值是否与集合的改写操作计数值一致。如果不一致,抛出一个ConcurrentModificationException 异常。

### 7. 最好不要用LinkedList.get(i)方法，效率很低：

链表不支持快速地随机访问。如果要查看链表中第n个元素，就必须从头开始， 越过n-1个元素。没有捷径可走。鉴于这个原因，在程序需要采用整数索引访问元素时， 程序员通常不选用链表。

绝对不应该使用这种让人误解的随机访问方法来遍历链表。下面这段代码的效率极低：
```java
for (int i = 0; i < list.size()；i++)
	do something with list.get(i);
```
每次査找一个元素都要从列表的头部重新开始搜索。LinkedList 对象根本不做任何缓存位置信息的操作。**对LinkedList遍历就用迭代器就行**。

如果发现自己正在使用这个方法`LinkedList.get(i)`，说明有可能对于所要解决的问题使用了错误的数据结构。

### 8. 散列表原理：

在Java 中，散列表用链表数组实现。每个列表被称为桶（ bucket)。要想査找表中对象的位置， 就要先计算它的散列码， 然后与桶的总数取余，所得到的结果就是保存这个元素的桶的索引。例如， 如果某个对象的散列码为76268, 并且有128 个桶，对象应该保存在第108 号桶中（ 76268除以128 余108 )。或许会很幸运， 在这个桶中没有其他元素， 此时将元素直接插人到桶中就可以了。当然，有时候会遇到桶被占的情况，这也是不可避免的。这种现象被称为散列冲突（ hash collision)。这时，需要用新对象与桶中的所有对象进行比较， 査看这个对象是否已经存在。如果散列码是合理且随机分布的， 桶的数目也足够大， 需要比较的次数就会很少。
（在JavaSE 8 中， 桶满时会从链表变为平衡二叉树。）
如果想更多地控制散列表的运行性能， 就要指定一个初始的桶数。桶数是指用于收集具有相同散列值的桶的数目。如果要插入到散列表中的元素太多， 就会增加冲突的可能性， 降低运行性能。如果大致知道最终会有多少个元素要插人到散列表中， 就可以设置桶数。通常， 将桶数设置为预计元素个数的75% ~ 150%。有些研究人员认为：尽管还没有确凿的证据，** 但最好将桶数设置为一个素数，以防键的集聚**。**标准类库使用的桶数是2的幂， 默认值为16(为表大小提供的任何值都将被自动地转换为2 的下一个幂)**。
当然，并不是总能够知道需要存储多少个元素的， 也有可能最初的估计过低。如果散列表太满， 就需要**再散列（rehashed)**。如果要对散列表再散列， 就需要创建一个桶数更多的表，并将所有元素插入到这个新表中，. 然后丢弃原来的表。**装填因子（load factor ) **决定何时对散列表进行再散列。例如， 如果装填因子为0.75 (默认值)，而表中超过75%的位置已经填入元素， 这个表就会用双倍的桶数自动地进行再散列。对于大多数应用程序来说， 装填因子为0.75 是比较合理的。

### 9. Queue 和 Deque

Java中LinkedList类实现了Queue接口，因此可以把LinkedList当成Queue来用。
`Queue<String> queue = new LinkedList<>();`

Ref：[Java 集合深入理解（9）：Queue 队列](https://blog.csdn.net/u011240877/article/details/52860924)
[Java 集合深入理解（10）：Deque 双端队列](https://blog.csdn.net/u011240877/article/details/52865173)

ArrayDeque 和 LinkedList 都实现了 Deque 接口，ArrayDeque 的效率要好些。
Ref: [Why is ArrayDeque better than LinkedList](https://stackoverflow.com/questions/6163166/why-is-arraydeque-better-than-linkedlist)
[What is the fastest Java collection with the basic functionality of a Queue?](https://stackoverflow.com/questions/6129805/what-is-the-fastest-java-collection-with-the-basic-functionality-of-a-queue)

**优先级队列（priority queue)** 中的元素可以按照任意的顺序插人，却总是按照排序的顺序进行检索。也就是说，无论何时调用remove 方法， 总会获得当前优先级队列中最小的元素。然而， 优先级队列并没有对所有的元素进行排序。如果用迭代的方式处理这些元素，并不需要对它们进行排序。
优先级队列使用了一个优雅且高效的数据结构，称为堆（ heap)。堆是一个可以自我调整的二叉树，对树执行添加（ add) 和删除（ remore) 操作， 可以让最小的元素移动到根，而不必花费时间对元素进行排序。
与TreeSet—样，一个优先级队列既可以保存实现了Comparable 接口的类对象， 也可以保存在构造器中提供的Comparator 对象。
使用优先级队列的典型示例是任务调度。每一个任务有一个优先级， 任务以随机顺序添加到队列中。每当启动一个新的任务时，都将优先级最高的任务从队列中删除（由于习惯上将1 设为“ 最高” 优先级，所以会将最小的元素删除)。
程序清单9-5 显示了一个正在运行的优先级队列。与TreeSet 中的迭代不同，这里的迭代并不是按照元素的排列顺序访问的。而删除却总是删掉剩余元素中优先级数最小的那个元素。

### 10. 有用但易忽略的点： 

> * Map 有一个`getOrDefault`方法，用于当Map中不存在某个键时，返回一个默认值。`getOrDefault(Object key, V defaultValue)(@since 1.8)`
> * `Map.putIfAbsent(K key, V value)(@since 1.8)`只有当Map中key存在且value不为null时，才会put
> * `Map.merge(K key, V value, BiFunction<? super V, ? super V, ? extends V> remappingFunction)（@since 1.8）`如果key 与一个非null 值v 关联， 将函数应用到v 和value, 将key 与结果关联， 或者如果结果为null , 则删除这个键。否则， 将key 与value 关联， 返回get(key)。
> * Map接口中还有很多有用的Default方法。。。（compute，computeIfPresent，computeIfAbsent，replaceAll 等）

### 11. LinkedHashMap的 访问顺序（Access Order）和 插入顺序（Insertion Order）

LinkedHashMap有一个这样的构造方法：`public LinkedHashMap(int initialCapacity, float loadFactor, boolean accessOrder)`

> initialCapacity   初始容量大小，使用无参构造方法时，此值默认是16
> loadFactor       加载因子，使用无参构造方法时，此值默认是 0.75f
> **accessOrder   false： 基于插入顺序;     true：  基于访问顺序** 

如果accessOrder为true的话，则会把访问过的元素放在链表后面，放置顺序是访问的顺序。
如果accessOrder为flase的话，则按插入顺序来遍历

accessOrder为true时，可用于实现LRU算法。

Ref： [详解LinkedHashMap如何保证元素迭代的顺序](http://www.php.cn/java-article-362041.html) (直接阅读“利用LinkedHashMap实现LRU算法缓存”部分即可)

WeakHashMap， Ref： [浅谈WeakHashMap](http://www.importnew.com/23182.html)

### 12. Enum、EnumMap、EnumSet、IdentityHashMap

Ref: [Enum、EnumMap、EnumSet的用法讲解](https://www.jianshu.com/p/3fed5f481e54)

**IdentityHashMap：**此类利用哈希表实现 Map 接口，比较键（和值）时使用引用相等性代替对象相等性。换句话说，在 IdentityHashMap 中，当且仅当 (k1==k2) 时，才认为两个键 k1 和 k2 相等（在正常 Map 实现（如 HashMap）中，当且仅当满足下列条件时才认为两个键 k1 和 k2 相等：(k1==null ? k2==null : e1.equals(e2))）。
Ref： http://www.cjsdn.net/Doc/JDK50/java/util/IdentityHashMap.html

### 13. Lambda表达式特殊的void兼容规则：

如果一个Lambda的**主体是一个语句表达式，它就和一个返回void的函数描述符兼容**（当然需要参数列表也兼容）。例如，以下两个都是合法的，尽管List的add方法返回了一个boolean，而不是Consumer上下文（T -> void）所要求的void：
```java
//Predicate返回了一个boolean
Predicate<String> p = s -> list.add(s);
//Consumer返回了一个void
Consumer<String> b = s -> list.add(s);
```

### 14. Lambda使用局部变量：

Lambda可以没有限制的捕获（也就是在其主体中引用）实例变量和静态变量。但局部变量必须显示声明为final，或事实上是final。
原因：实例变量存储在堆中，而堆是在线程之间共享的。而局部变量则保存在栈上。如果Lambda可以直接访问局部变量，而且Lambda是在一个线程中使用的，则使用Lambda的线程，可能会在分配该变量的线程将这个变量收回之后，去访问该变量。因此，java在访问自由局部变量时，实际上是在访问它的副本，而不是访问原始变量。

### 15. 实现自己的收集器Collector

[实现自己的收集器例子，ToListCollector](https://github.com/davidj0512/2_JavaFundamentals/blob/master/2a_implement_custom_Collector.md)
