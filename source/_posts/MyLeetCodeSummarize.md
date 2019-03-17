---
title: LeetCode刷题总结（持续更新）
date: 2019/3/15
updated: 2019/3/17 
tags:
    - LeetCode
categories:
    - 技术总结
---
我的答案仓库地址：[MyLeetCode](https://github.com/fangmiao97/MyLeetCode)
## Tree与迭代、动态规划
* [Maximum Binary Tree](https://leetcode.com/problems/maximum-binary-tree/)
    * 思路：将创建最大二叉树，即根节点比所有叶子节点都大，的过程，分解成先寻找到当前数列中最大值，然后在创建左右最大子树的过程
    * 退出情况是数列为一个数时返回null，即表示结束没有子树可以构造了

```java
TreeNode root = new TreeNode(nums[max_i]);
root.left = construct(nums, l, max_i);
root.right = construct(nums, max_i + 1, r);
```
* [Maximum Binary Tree II](https://leetcode.com/problems/maximum-binary-tree-ii/)
    * 描述：在一棵现成的最大二叉树中，插入一个数，使得插入之后依然是最大二叉树
    * 技巧点：比根节点小的数一律往根节点的右子树插。
    * 思路：插入值与当前根节点的值比较，若大于根节点的值则创建节点，并将原根节点作为自己的左子结点，并返回新的根节点。否则的话插入值插入原根节点的右子树，并循环这个过程。若插入值比较到最后，即与null比较，则创建该节点并返回这个节点。

```java
public TreeNode insertIntoMaxTree(TreeNode root, int val) {
        if(root == null){
            return new TreeNode(val);
        }
        if(root.val<val){
            TreeNode head = new TreeNode(val);
            head.left = root;
            return head;
        }
        root.right = insertIntoMaxTree(root.right, val);
        return root;
    }
```
* [Second Minimum Node In a Binary Tree](https://leetcode.com/problems/second-minimum-node-in-a-binary-tree/)
    * 需要注意的事，必须将所有的节点都遍历完全才能找到第二小的数字。因为有一个用例将第二小的数值藏在了最下面
    * 采用DFS或BFS都可以。关键是判断第二小的时候，先要将第一小的数找到，如果之后有数字不是小于**等于**第一小的话，才可以比较是不是第二小。如果只是单纯的小于第一小，会让第二小也成为和第一小一样的数值。
* BFS常用结构

```java
        Queue<TreeNode> a = new LinkedList<>();
        a.offer(p);
        while (!a.isEmpty()){
            for (int sz = a.size(); sz > 0; --sz) {
                TreeNode n = a.poll();
                //do something
                a.offer(n.left);
                a.offer(n.right);
            }
```
## 字节/位处理
* [Reverse Integer](https://leetcode.com/problems/reverse-integer/)
    * java中不同数据类型的取值范围
        * int 32
        * short 16
        * long 64
        * float 32
        * double 64
    * 本题中关于溢出可能的判断
   
```java
//方式一
//正数溢出情况（2147483647）
if (rev > Integer.MAX_VALUE/10 || (rev == Integer.MAX_VALUE / 10 && pop > 7)) return 0;
//负数（-2147483648）
if (rev < Integer.MIN_VALUE/10 || (rev == Integer.MIN_VALUE / 10 && pop < -8)) return 0;


//方式二
if((rev - pop) / 10 != org) return 0;//溢出的话肯定计算不出原来的数字了
```

* [Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/)
    * 找一个int数的二进制中有多少个1，hamming weight
    * & -- 位与运算 | -- 位或运算

```java
int res = 0;
        for(int i = 31; i >= 0; i--) {
            if((n & 1) == 1)
                res++;
            n >>= 1; //右移一位
        }
```
* [java中的移位操作符](https://zhuanlan.zhihu.com/p/30108890)

* [Reverse Bits](https://leetcode.com/problems/reverse-bits/)
    * 翻转32位比特值，依次移动每一位。将每次需要移动的比特放在最后一位。与1进行位与操作后，将这一位移动到正确的位置后，与结果进行位或操作。
    
```java
int ans = 0;
        for(int i = 31; i >= 0; i--) {
            ans = ans | ((n & 1) << i);
            n >>= 1;
        }
```

* [Counting Bits](https://leetcode.com/problems/counting-bits/)
    * **DP** When it comes to even numbers, i.e, 2,4,6,8, their binary should be like '10', '100', '110', '1000' 
      so one notable difference is their unit digit is always 0, 
      which means when you call >> 1- shift one bit rightwards 
      and also means dividing by 2- would cause no change to the count of '1' in the binary string.
      
      Vice versa, when you meet odd numbers, shifting one bit rightwards always eliminates one '1' digit from original binary string,
      that is why we should "compensate" one '1' character to the count.
      
      To sum up, when you meet even number the count of '1's is always the same as its half number,
      on the other hand, remember to plus one to the odds' half number.
   
```java
    int[] f = new int[num + 1];
    for (int i=1; i<=num; i++) f[i] = f[i >> 1] + (i & 1);
    return f;
```

