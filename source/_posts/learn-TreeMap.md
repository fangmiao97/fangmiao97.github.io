---
title: TreeMap-红黑树在java中的实现
date: 2019/07/22
categories: 
    - 算法
---
## 参考

* [简书](https://www.jianshu.com/p/2dcff3634326)

## 红黑树

- 树中每个节点必须是有颜色的，要么红色，要么黑色；

- 树中的根节点必须是黑色的；

- 树中的叶节点必须是黑色的，也就是树尾的NIL节点或者为null的节点；

- 树中任意一个节点如果是红色的，那么它的两个子节点一点是黑色的；

- 任意节点到叶节点（树最下面一个节点）的每一条路径所包含的黑色节点数目一定相同；

![](https://pic3.superbed.cn/item/5df9e53d76085c32898ed7bc.jpg)

* 增删改查时间复杂度 O(logn)
* key不重复，不为null
## TreeMa

### 使用

* 可以自行实现Comparator来进行比较，这个在插入，搜索等方法是都会用到。不自行实现的话，就是自然比较。

```java
public interface NavigableMap<K,V> extends SortedMap<K,V> {

    //返回小于key的第一个元素：
    Map.Entry<K,V> lowerEntry(K key);

    //返回小于key的第一个键：
    K lowerKey(K key);

    //返回小于等于key的第一个元素：
    Map.Entry<K,V> floorEntry(K key);

    //返回小于等于key的第一个键：
    K floorKey(K key);

    //返回大于或者等于key的第一个元素：
    Map.Entry<K,V> ceilingEntry(K key);

    //返回大于或者等于key的第一个键：
    K ceilingKey(K key);

    //返回大于key的第一个元素：
    Map.Entry<K,V> higherEntry(K key);

    //返回大于key的第一个键：
    K higherKey(K key);

    //返回集合中第一个元素：
    Map.Entry<K,V> firstEntry();

    //返回集合中最后一个元素：
    Map.Entry<K,V> lastEntry();

    //返回集合中第一个元素，并从集合中删除：
    Map.Entry<K,V> pollFirstEntry();

    //返回集合中最后一个元素，并从集合中删除：
    Map.Entry<K,V> pollLastEntry();

    //返回倒序的Map集合：
    java.util.NavigableMap<K,V> descendingMap();

    NavigableSet<K> navigableKeySet();

    //返回Map集合中倒序的Key组成的Set集合：
    NavigableSet<K> descendingKeySet();

    java.util.NavigableMap<K,V> subMap(K fromKey, boolean fromInclusive,
                                       K toKey, boolean toInclusive);

    java.util.NavigableMap<K,V> headMap(K toKey, boolean inclusive);

    java.util.NavigableMap<K,V> tailMap(K fromKey, boolean inclusive);

    SortedMap<K,V> subMap(K fromKey, K toKey);

    SortedMap<K,V> headMap(K toKey);

    SortedMap<K,V> tailMap(K fromKey);
}
```

* 节点结构

```java
static final class Entry<K,V> implements Map.Entry<K,V> {
    //插入节点的key:
    K key;

    //插入节点的value:
    V value;

    //插入节点的左子节点：
    java.util.TreeMap.Entry<K,V> left = null;

    //插入节点的右子节点：
    java.util.TreeMap.Entry<K,V> right = null;

    //插入节点的父节点：
    java.util.TreeMap.Entry<K,V> parent;

    //插入节点默认的颜色：
    boolean color = BLACK;

    //Entry对象的构造函数：
    Entry(K key, V value, java.util.TreeMap.Entry<K,V> parent) {
        this.key = key;
        this.value = value;
        this.parent = parent;
    }
    public K getKey() {
        return key;
    }
    public V getValue() {
        return value;
    }
    public V setValue(V value) {
        V oldValue = this.value;
        this.value = value;
        return oldValue;
    }
    public boolean equals(Object o) {
        if (!(o instanceof Map.Entry))
            return false;
        Map.Entry<?,?> e = (Map.Entry<?,?>)o;
        return valEquals(key,e.getKey()) && valEquals(value,e.getValue());
    }
    public int hashCode() {
        int keyHash = (key==null ? 0 : key.hashCode());
        int valueHash = (value==null ? 0 : value.hashCode());
        return keyHash ^ valueHash;
    }
    public String toString() {
        return key + "=" + value;
    }
}
```

* TreeMap元素添加

```java
//插入key-value:
public V put(K key, V value) {
    //根节点赋值给t:
    java.util.TreeMap.Entry<K,V> t = root;
    //如果根节点为null，则创建第一个节点，根节点
    if (t == null) {
        //对传入的元素进行比较，若TreeMap没定义了Comparator，则验证传入的元素是否实现了Comparable接口；
        compare(key, key);
        //根节点赋值：
        root = new java.util.TreeMap.Entry<>(key, value, null);
        //长度为1：
        size = 1;
        //修改次数+1
        modCount++;
        //直接返回：此时根节点默认为黑色
        return null;
    }

    //如果根节点不为null:
    int cmp;
    java.util.TreeMap.Entry<K,V> parent;
    Comparator<? super K> cpr = comparator;

    //判断TreeMap中自定义比较器comparator是否为null：
    if (cpr != null) {
        // do while循环，查找新插入节点的父节点：
        do {
            // 记录上次循环的节点t，首先将根节点赋值给parent:
            parent = t;
            //利用自定义比较器的比较方法：传入的key跟当前遍历节点比较：
            cmp = cpr.compare(key, t.key);
            //判断结果小于0，处于父节点的左边 
            if (cmp < 0)
                t = t.left;
            else if (cmp > 0)
            //判断结果大于0，处于父节点的右边 
                t = t.right;
            else
            //判断结果等于0，为当前节点，覆盖原有节点处的value：
                return t.setValue(value);
        // 只有当t为null，也就是找到了新节点的parent了
        } while (t != null);
    } else {
        //没有自定义比较器：
        if (key == null)
            //TreeMap不允许插入key为null，抛异常：
            throw new NullPointerException();
        //将key转换为Comparable对象：若key没有实现Comparable接口，此处会报错
        Comparable<? super K> k = (Comparable<? super K>) key;
        //同上：寻找新增节点的父节点：
        do {
            parent = t;
            cmp = k.compareTo(t.key);
            if (cmp < 0)
                t = t.left;
            else if (cmp > 0)
                t = t.right;
            else
                return t.setValue(value);
        } while (t != null);
    }

    //构造新增节点对象：
    java.util.TreeMap.Entry<K,V> e = new java.util.TreeMap.Entry<>(key, value, parent);

    //根据之前的比较结果，判断新增节点是在父节点的左边还是右边
    if (cmp < 0)
        // 如果新节点key的值小于父节点key的值，则插在父节点的左侧
        parent.left = e;
    else
        // 如果新节点key的值大于父节点key的值，则插在父节点的右侧
        parent.right = e;
    //核心方法：插入新的节点后，为了保持红黑树平衡，对红黑树进行调整
    fixAfterInsertion(e);
    size++;
    modCount++;
    return null;
}

//对插入的元素比较：若TreeMap没有自定义比较器，则调用调用默认自然顺序比较，要求元素必须实现Comparable接口；
//若自定义比较器，则用自定义比较器对元素进行比较；
final int compare(Object k1, Object k2) {
    return comparator==null ? ((Comparable<? super K>)k1).compareTo((K)k2)
            : comparator.compare((K)k1, (K)k2);
}

//核心方法：维护TreeMap平衡的处理逻辑；（回顾上面的图文描述）
private void fixAfterInsertion(java.util.TreeMap.Entry<K,V> x) {
    //首先将新插入节点的颜色设置为红色
    x.color = RED;
    //TreeMap是否平衡的重要判断，当不在满足循环条件时，代表树已经平衡；
    //x不为null，不是根节点，父节点是红色（三者均满足才进行维护）：
    while (x != null && x != root && x.parent.color == RED) {
        //节点x的父节点 是 x祖父节点的左孩子：
        if (parentOf(x) == leftOf(parentOf(parentOf(x)))) {
            //获取x节点的叔叔节点y:
            java.util.TreeMap.Entry<K,V> y = rightOf(parentOf(parentOf(x)));
            
            //叔叔节点y是红色：
            if (colorOf(y) == RED) {
                //将x的父节点设置黑色：
                setColor(parentOf(x), BLACK);  
                //将x的叔叔节点y设置成黑色：
                setColor(y, BLACK);
                //将x的祖父节点设置成红色：
                setColor(parentOf(parentOf(x)), RED);
                //将x节点的祖父节点设置成x(进入了下一次判断)：
                x = parentOf(parentOf(x));
            } else {
                 //叔叔节点y不为红色：
                //x为其父节点的右孩子：
                if (x == rightOf(parentOf(x))) {
                    x = parentOf(x);
                    rotateLeft(x);
                }
                setColor(parentOf(x), BLACK);
                setColor(parentOf(parentOf(x)), RED);
                //右旋：
                rotateRight(parentOf(parentOf(x)));
            }
        } else {
            //节点x的父节点 是x祖父节点的右孩子：

            //获取x节点的叔叔节点y:
            java.util.TreeMap.Entry<K,V> y = leftOf(parentOf(parentOf(x)));
            //判断叔叔节点y是否为红色：
            if (colorOf(y) == RED) {
                setColor(parentOf(x), BLACK);12
                setColor(y, BLACK);5
                setColor(parentOf(parentOf(x)), RED);10
                x = parentOf(parentOf(x));
            } else {
                if (x == leftOf(parentOf(x))) {
                    x = parentOf(x);
                    rotateRight(x);
                }
                setColor(parentOf(x), BLACK);
                setColor(parentOf(parentOf(x)), RED);
                //左旋：
                rotateLeft(parentOf(parentOf(x)));
            }
        }
    }
    root.color = BLACK;
}

//获取节点的颜色：
private static <K,V> boolean colorOf(java.util.TreeMap.Entry<K,V> p) {
    //节点为null,则默认为黑色；
    return (p == null ? BLACK : p.color);
}

//获取p节点的父节点：
private static <K,V> java.util.TreeMap.Entry<K,V> parentOf(java.util.TreeMap.Entry<K,V> p) {
    return (p == null ? null: p.parent);
}

//对节点进行着色，TreeMap使用了boolean类型来代表颜色（true为红色，false为黑色）
private static <K,V> void setColor(java.util.TreeMap.Entry<K,V> p, boolean c){
    if (p != null)
        p.color = c;
}

//获取左子节点：
private static <K,V> java.util.TreeMap.Entry<K,V> leftOf(java.util.TreeMap.Entry<K,V> p) {
    return (p == null) ? null: p.left;
}

//获取右子节点：
private static <K,V> java.util.TreeMap.Entry<K,V> rightOf(java.util.TreeMap.Entry<K,V> p) {
    return (p == null) ? null: p.right;
}

//左旋：
private void rotateLeft(java.util.TreeMap.Entry<K,V> p) {
    if (p != null) {
        java.util.TreeMap.Entry<K,V> r = p.right;
        p.right = r.left;
        if (r.left != null)
            r.left.parent = p;
        r.parent = p.parent;
        if (p.parent == null)
            root = r;
        else if (p.parent.left == p)
            p.parent.left = r;
        else
            p.parent.right = r;
        r.left = p;
        p.parent = r;
    }
}

//右旋：
private void rotateRight(java.util.TreeMap.Entry<K,V> p) {
    if (p != null) {
        java.util.TreeMap.Entry<K,V> l = p.left;
        p.left = l.right;
        if (l.right != null) l.right.parent = p;
        l.parent = p.parent;
        if (p.parent == null)
            root = l;
        else if (p.parent.right == p)
            p.parent.right = l;
        else p.parent.left = l;
        l.right = p;
        p.parent = l;
    }
}
```