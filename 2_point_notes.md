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

### 2. 