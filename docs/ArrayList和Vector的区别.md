### ArrayList和Vector的区别

+ 历史时间上：ArrayList是JDK1.2提供的，Vector是JDK1.0就提供的。
+ 处理形式：ArrayList异步处理，性能更高；Vector同步处理，性能低。
+ 数据安全：ArrayList非线程安全；Vector线程安全。

##### TIPS：为什么说ArrayList非线程安全，而Vector线程安全呢？

​	我们来看一下JDK源码，首先是ArrayList的add、set、get方法：

```java
...
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
...
    
public E set(int index, E element) {
    rangeCheck(index);

    E oldValue = elementData(index);
    elementData[index] = element;
    return oldValue;
}
....

public E get(int index) {
    rangeCheck(index);

    return elementData(index);
}
...
```

​	再来看一下线程安全的Vector的add、set、get方法：

```java
...
public synchronized boolean add(E e) {
    modCount++;
    ensureCapacityHelper(elementCount + 1);
    elementData[elementCount++] = e;
    return true;
}
...

public synchronized E set(int index, E element) {
    if (index >= elementCount)
        throw new ArrayIndexOutOfBoundsException(index);

    E oldValue = elementData(index);
    elementData[index] = element;
    return oldValue;
}
....
    
public synchronized E get(int index) {
    if (index >= elementCount)
        throw new ArrayIndexOutOfBoundsException(index);

    return elementData(index);
}
....
```

所以原因就有了，因为Vector的这几个方法中都用了synchronize关键字来修饰，加了锁，而ArrayList并没有这样做。

