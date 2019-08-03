---
title: priorityQueue优先队列/堆
date: 2019/8/3 
tags:
    - Java
categories:
    - 技术总结
---
## context

如果对一串对象的操作每次都要根据一定的顺序来完成，需要每次操作之后都排序吗？

比如，用数组存储一种类型的对象，然后自己写Comparator，使用Arrays的sort方法来进行排序。如下：

```java
    static class Tower {
        int height;
        int index;
        public Tower(int height, int index) {
            this.height = height;
            this.index = index;
        }
    }
 
    public static class TowerComparator implements Comparator<Tower> {
        public int compare(Tower t1, Tower t2) {
            return t1.height - t2.height;
        }
    }

    Tower[] towers = new Tower[n];
    Arrays.sort(towers, new TowerComparator())
```

此外还可以使用priorityQueue，在队列创建时就可以生成有序的队列。

## priorityQueue

列如：[塔](https://www.nowcoder.com/questionTerminal/54868056c5664586b121d9098d008719)

```java
import java.util.Comparator;
import java.util.PriorityQueue;
import java.util.Scanner;
 
class Tal {
    int h, index;
 
    Tal(int h, int index) {
        this.h = h;
        this.index = index;
    }
}
 
public class Main {
    /**
     * 思想：借助大小优先队列每次取出最高塔和最低的塔进行操作，操作完之后再放回去
     * 直至出现最大最小塔的高度差为1或者可用的移动次数用完。
     */
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();//塔的数量
        int k = sc.nextInt();//最多操作次数
        PriorityQueue<Tal> minque = new PriorityQueue<>(n, new Comparator<Tal>() {
            @Override
            public int compare(Tal o1, Tal o2) {
                return o1.h - o2.h;
            }
        });
        PriorityQueue<Tal> maxque = new PriorityQueue<>(n, new Comparator<Tal>() {
            @Override
            public int compare(Tal o1, Tal o2) {
                return o2.h - o1.h;
            }
        });
        Tal tal = null;
        for (int i = 1; i <= n; i++) {
            int t = sc.nextInt();
            tal = new Tal(t, i);
            minque.add(tal);
            maxque.add(tal);
        }
        int s = 0;//最小稳定值
        int times = 0;//移动次数
        //记录移动的索引
        int[][] move = new int[k][2];
        while (times < k) {
            Tal min = minque.poll();//最低的塔
            Tal max = maxque.poll();//最高的塔
            //如果最低塔和最高塔高度相等或者相差为1，就没必要继续了
            if (max.h - min.h <= 1) {
                break;
            }
            //最低塔高度增加1，最高塔高度减少1
            min.h = min.h + 1;
            max.h = max.h - 1;
            move[times][0] = max.index;
            move[times][1] = min.index;
            minque.add(min);
            maxque.add(max);
            times++;
        }
        s = maxque.peek().h - minque.peek().h;
        System.out.println(s + " " + times);
        for (int i = 0; i < times; i++) {
            System.out.println(move[i][0] + " " + move[i][1]);
        }
    }
}
```

* [Java 优先级队列 PriorityQueue](https://www.jianshu.com/p/c577796e537a)