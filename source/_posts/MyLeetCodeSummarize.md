---
title: LeetCode刷题总结（持续更新）
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