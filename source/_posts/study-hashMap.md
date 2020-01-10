---
title: hashMap学习
date: 2020/01/08
categories:
    - Java
tags:
    - hashMap
---

## context

面试必问HashMap哈，之前有被问到：

* 为什么用红黑树不是平衡二叉树？
* 为什么是链表大于8的时候，变成红黑树？

## 1.8之前

采用数组和链表实现，hash值计算的是存储在哪一个数组节点里（桶），相同的节点里面是链表。也就是说链表是hash冲突的数据。每个节点是一个entry<k,v>，对key计算hash。

![](https://pic.downk.cc/item/5e153d7076085c32895df883.jpg)

如图所示，发生hash冲突时，需要在每个链表里面找需要的数据，时间O(n)，这样链表很长的话效率就不会很好。

## 1.8之后

![](https://pic.downk.cc/item/5e153ea876085c32895e1af8.jpg)

### 链表节点node

```
final int hash;
    final K key;
    V value;
    Node<k,v> next;
 ```


### hash计算

```
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
    
public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }
```

首先获取对象的hashCode()值，然后将hashCode值右移16位，然后将右移后的值与原来的hashCode做异或运算，返回结果。（其中h>>>16，在JDK1.8中，优化了高位运算的算法，使用了零扩展，无论正数还是负数，都在高位插入0）。

在putVal源码中，我们通过(n-1)&hash获取该对象的键在hashmap中的位置。（其中hash的值就是（1）中获得的值）其中n表示的是hash桶数组的长度，并且该长度为2的n次方，这样(n-1)&hash就等价于hash%n。因为&运算的效率高于%运算。

putVal(hash(key), key, value, false, evict)//把元素放到hashMap中

 if ((p = tab[i = (n - 1) & hash]) == null)//获取位置
        tab[i] = newNode(hash, key, value, null);


tab即是table，n是map集合的容量大小，hash是上面方法的返回值。因为通常声明map集合时不会指定大小，或者初始化的时候就创建一个容量很大的map对象，所以这个通过容量大小与key值进行hash的算法在开始的时候只会对低位进行计算，虽然容量的2进制高位一开始都是0，但是key的2进制高位通常是有值的，因此先在hash方法中将key的hashCode右移16位在与自身异或，使得高位也可以参与hash，更大程度上减少了碰撞率。

![](https://pic.downk.cc/item/5e154fcf76085c3289600fb2.jpg)

![](https://pic.downk.cc/item/5e15507676085c3289601fb6.jpg)



### 红黑树节点TreeNode

红黑树比链表多了四个变量，parent父节点、left左节点、right右节点、prev上一个同级节点

### hashMap类属性

```
public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Cloneable, Serializable {
    // 序列号
    private static final long serialVersionUID = 362498820763181265L;    
    // 默认的初始容量是16
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;   
    // 最大容量
    static final int MAXIMUM_CAPACITY = 1 << 30; 
    // 默认的填充因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    // 当桶(bucket)上的结点数大于这个值时会转成红黑树
    static final int TREEIFY_THRESHOLD = 8; 
    // 当桶(bucket)上的结点数小于这个值时树转链表
    static final int UNTREEIFY_THRESHOLD = 6;
    // 桶中结构转化为红黑树对应的table的最小大小
    static final int MIN_TREEIFY_CAPACITY = 64;
    // 存储元素的数组，总是2的幂次倍
    transient Node<k,v>[] table; 
    // 存放具体元素的集
    transient Set<map.entry<k,v>> entrySet;
    // 存放元素的个数，注意这个不等于数组的长度。
    transient int size;
    // 每次扩容和更改map结构的计数器
    transient int modCount;   
    // 临界值 当实际大小(容量*填充因子)超过临界值时，会进行扩容
    int threshold;
    // 填充因子
    final float loadFactor;
}
```

### 为什么是threshold是8？

源码注释里面有写，说随机的hashcode落入到bucket（数组）中是成泊松分布的，节点链表大于8的概率是0.00000006。
另外红黑树的空间占用更多，所以为了权衡trade off，选择了8为默认阈值。

网上面有另一种说法，说的是8各节点的红黑树平均查找是log8 = 3，而8长度的链表是4，所以就转变了。



