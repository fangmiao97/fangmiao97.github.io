---
title: 学习KMP && Sunday算法
date: 2019/05/03
categories: 
    - 算法
---
## 引入
* [Implement strStr()](https://leetcode.com/problems/implement-strstr/)
    * LeetCode实现String.indexOf()，即返回子串在字符串中第一次出现的索引位置，没有则返回-1，子串为空则返回0
* 暴力解法
    * 从0位置开始，比对与子串是否相同，出现不同则在字符串中往后移一位，再次比较子串，直到找到。
    * 复杂度
        * 设字符串长度为m，要查找的子串长度为n
        * 时间 O(m*n)
    * 问题
        * 每次出现不一致的情况，只向后移动一位，再从头开始比对，很多没有必要
        * 如：在aaaaaaaaaaaab,查找aaaab

![](https://puui.qpic.cn/fans_admin/0/3_1500912214_1556864943460/0)

![](https://puui.qpic.cn/fans_admin/0/3_1486517634_1556864983556/0)

![](https://puui.qpic.cn/fans_admin/0/3_308750603_1556864998323/0)

## KMP
* 概念
    * KMP算法要解决的问题就是在字符串（也叫主串）中的模式（pattern）定位问题。说简单点就是我们平时常说的关键字搜索。模式串就是关键字（接下来称它为P），如果它在一个主串（接下来称为T）中出现，就返回它的具体位置，否则返回-1（常用手段）。

* 对于暴力破解的优化思考
    * 主串不要每次只移一位，可以让它（i）保持不动，移动模式中j的位置
    * 利用已经部分匹配这个有效信息，保持i指针不回溯，通过修改j指针，让模式串尽量地移动到有效的位置
    
![](https://puui.qpic.cn/fans_admin/0/3_308750603_1556865032698/0)

* j要怎么移动
    * 当匹配失败时，j要移动的下一个位置k。存在着这样的性质：最前面的k个字符和j之前的最后k个字符是一样的

![](https://puui.qpic.cn/fans_admin/0/3_1486517634_1556865346487/0)

![](https://puui.qpic.cn/fans_admin/0/3_771348268_1556865899518/0)

![](https://puui.qpic.cn/fans_admin/0/3_1268522869_1556866125545/0)

![](https://puui.qpic.cn/fans_admin/0/3_122902507_1556866189608/0)

* 数学表示
    * 如果k表示不匹配时，j要移动的位置
    * P[0 ~ k-1] == P[j-k ~ j-1]
    
    ![](https://puui.qpic.cn/fans_admin/0/3_1268522869_1556866470014/0)
    
    * 当T[i] != P[j]时
      
      有T[i-j ~ i-1] == P[0 ~ j-1]
      
      由P[0 ~ k-1] == P[j-k ~ j-1]
      
      必然：T[i-k ~ i-1] == P[0 ~ k-1]
      
    * 因此可以直接将j移动到k而无须再比较前面的k个字符

* 怎么找到不匹配时j要移动的k位置
    * 模式串中每个位置发生不匹配时，所要移动到的k位置都是不同的，每个位置都要计算
    * 使用next数据来保存模式串中每个位置的k值
    * next[j]的值（也就是k）表示，当P[j] != T[i]时，j指针的下一步移动位置
    * 当j为0时，如果这时候不匹配
        * j已经在最左边了，不可能再移动了，这时候要应该是i指针后移。有next[0] = -1
        
        ![](https://puui.qpic.cn/fans_admin/0/3_912665407_1556869374669/0)
    
    * 当j为1
        * j指针一定是后移到0位置的
        
        ![](https://puui.qpic.cn/fans_admin/0/3_767595719_1556869496396/0)
        
    * 其他
        * 当P[k] == P[j]时
        * next[j+1] == next[j] + 1
        
        ![](https://puui.qpic.cn/fans_admin/0/3_912665407_1556869639696/0)
        
        ![](https://puui.qpic.cn/fans_admin/0/3_912665407_1556869662493/0)
        
        * P[k] != P[j]
        * k = next[k]
        
        ![](https://puui.qpic.cn/fans_admin/0/3_912665407_1556869752648/0)
        
        ![](https://puui.qpic.cn/fans_admin/0/3_912665407_1556870613284/0)
        
## 参考

* https://www.cnblogs.com/yjiyjige/p/3263858.html#top

## 代码实现

```java
class Solution {
    public int strStr(String haystack, String needle) {
        
        if(needle.length() == 0)
            return 0;
        
        char[] t = haystack.toCharArray();
        char[] p = needle.toCharArray();
        
        int[] next = getNext(needle);
        
        int i = 0, j = 0;
        while(i < t.length && j < p.length) {
            if(j == -1 || t[i] == p[j]) {
                i++;
                j++;
            }else
                j = next[j];
        }
        
        if(j == p.length) {
            return i - j;
        }else
            return -1;
          
    }
    
    public int[] getNext(String needle) {
        
        char[] p = needle.toCharArray();
        int[] next = new int[p.length];
        
        next[0] = -1;
        
        int j = 0;
        int k = -1;
        
        while(j < p.length - 1) {//减一是因为下面会有++j
            if(k == -1 || p[j] == p[k]) {//k==-1是next[1]=0
                if(p[++j] == p[++k])
                    next[j] = next[k];
                else
                    next[j] = k;
            }else {
                k = next[k];
            }
        }
        
        return next;
    }
}
```

## Sunday算法

[字符串匹配算法之Sunday算法](https://www.jianshu.com/p/2e6eb7386cd3)

```java
class Solution {
    public int strStr(String haystack, String needle) {
        int m = haystack.length(), n = needle.length();
        int[] occ = getOCC(needle);
        int jump = 0;//不能匹配时，匹配位置向后移动的长度
        for(int i = 0; i <= m - n; i += jump) {
            int j = 0;//模式串中已经匹配到的位置
            while(j < n && haystack.charAt(i + j) == needle.charAt(j))
                j++;//当前这一位匹配成功
            if(j == n)
                return i;
            jump = i + n < m ? n - occ[haystack.charAt(i + n)] : 1;
        }
        return -1;
    }
    
    public int[] getOCC(String p) {
        int[] occ = new int[128];
        Arrays.fill(occ, -1);
        for(int i = 0; i < p.length(); i++) {
            occ[p.charAt(i)] = i;//从串尾到最后出现该字符的长度 
        }
        return occ;
    }
    
}
```

    
    

    