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

## Two Pointers

* [浅析经典面试算法题-two pointer的运用](https://chocoluffy.com/2016/12/04/浅析经典面试算法题-two-pointer的运用/)

* Two Sum
    * integer array已经过排序
    * 两个pointers一头一尾。那么sum只有三种可能：
        * sum == target，则返回
        * sum < target，头指针向后走一个
        * sum > target，尾指针向前走一个
    * 循环条件，头 < 尾
    
```java
int low = 0;
int high = nums.length - 1;
while(low < high) {
    if(nums[low] + nums[high] == target)
        //do something;
        // low++ high--;
    else if (nums[low] + nums[high] < target)
        low++;
    else
        high--;
} 
```

* 3Sum
    * 先将数列排序，再固定第一个数字，从剩下的数列中用2 sum的方法找。
    * 注意一些要过滤的条件：
        * 第一个数字在移动的过程中，如果与前一个一样的话，就再移一下
        * low和high移动的道理也一样
 
```java
 public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<>()
        for(int i = 0; i < nums.length - 2; i++) {
            if(i == 0 || nums[i] != nums[i - 1]) {
                int low = i + 1;
                int high = nums.length - 1;
                int remain = 0 - nums[i];
                while(low < high) {
                    if(nums[low] + nums[high] == remain) {
                        res.add(Arrays.asList(nums[i], nums[low], nums[high]));
                        while(low < high && nums[low + 1] == nums[low])
                            low++;
                        while(low < high && nums[high - 1] == nums[high])
                            high--;
                        low++;
                        high--;
                    }else if(nums[low] + nums[high] < remain)
                        low++;
                    else
                        high--;
                }
            }
        }
        return res;
    }
```

* 3Sum closest
    * 找最接近的sum（也可能相等）
    * 增加判断条件Math.abs小的话，就要更新。

* 3Sum With Multiplicity
    * 每一中情况都要考虑到--排列组合

```java
  if (A[j] == A[k]) {
    // If A[j...k] are all equal, then there are C(k - j + 1, 2) 
    // combinations that meet the requirement.取两个
    res = (res + (k - j + 1) * (k - j) / 2) % m;
    break;
    }
    int l = 1, r = 1;
    while (j + l < k && A[j + l] == A[j]) { ++l; } // l: number of elements equal to A[j].
    while (j < k - r && A[k - r] == A[k]) { ++r; } // r: number of elements equal to A[k].
    res = (res + l * r) % m; // found l * r cases that meet the requirement.
    j += l; // forward j by l steps.
    k -= r; // backward k by r steps.
```
## 基础字符串操作
* reverse
```java
 public String reverse(String s) {
      StringBuilder res=new StringBuilder();
        for (int i = 0; i < s.length(); i++)
            res.insert(0,s.charAt(i));
        return res.toString();
    }
```

* split 对应API public String[] split(String regex, int limit)

```java
public String[] split(String s) {
        ArrayList < String > words = new ArrayList < > ();
        StringBuilder word = new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == ' ') {
                words.add(word.toString());
                word = new StringBuilder();
            } else
                word.append( s.charAt(i));
        }
        words.add(word.toString());
        return words.toArray(new String[words.size()]);
    }
```
* 大小写转换
    * a - 97 0x61(0110 0001) A - 65 0x41(0100 0001)
    
```java
    //法一
    string.toLowerCase() or toUpperCase()
    //法二
    if ('A' <= a[i] && a[i] <= 'Z')
        a[i] = (char) (a[i] - 'A' + 'a');
    //法三，按位或，把第五位的1加上
    char c = (char)(str.charAt(i) | (char)(32));
```

## 非常规顺序操作

* [ZigZag Conversion](https://leetcode.com/problems/zigzag-conversion/)
    * 取相同数字位的进行操作：0123210123210..
    * 法一：能够发现0之后都是加一，3之后都是减一
    * 设置一个标志来判断是否加一还是减一
    * 法二：变步长
    
```java
    //1
    if(curRow == 0 || curRow == numRows - 1) goingDown = !goingDown;
    curRow += goingDown ? 1 : -1;
    //2
            int n = s.length();
            int cycleLen = 2 * numRows - 2; //numRows = 4
    
            for (int i = 0; i < numRows; i++) {
                for (int j = 0; j + i < n; j += cycleLen) {
                    ret.append(s.charAt(j + i));
                    if (i != 0 && i != numRows - 1 && j + cycleLen - i < n)
                        ret.append(s.charAt(j + cycleLen - i));
                }
            }
```