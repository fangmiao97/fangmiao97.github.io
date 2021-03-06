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
* [Sum of Root To Leaf Binary Numbers](https://leetcode.com/problems/sum-of-root-to-leaf-binary-numbers/)
    * DFS
    
```java
    public int sumRootToLeaf(TreeNode root) {
        return dfs(root, 0);
    }
    public static int dfs(TreeNode root, int sum) {        
        if(root == null) return 0;
        sum = sum * 2 + root.val;
        return root.left == root.right ? sum : dfs(root.left, sum) + dfs(root.right, sum);
        
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

* Sum of Square Numbers
    * c = a^2 + b^2
    
```java
public boolean judgeSquareSum(int c) {
        int limit = (int)Math.sqrt(c);
        int low = 0;
        while(low<=limit){
            int sum = low*low + limit*limit;
            if(c==sum){
                return true;
            }
            if(sum<c){
               low++;
            } else {
               limit--; 
            }
        }
        return false;
    }
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

* 相同前缀
    * indexOf（int，ch）：先看第一个indexOf它返回值是int，在看它的参数（int，ch）意思就是使用者可以给参数一个‘char’字符所代表的int值，然后去从前向后找到该字符在字符串中第一次出现处的索引，当然了我们不可能记得住每一个char的值所以我们在使用时直接用String s=abcdef;　int i=s.indexOf('d')
    这种方式就可以了，char类型会自动提升为int类型，还有就是要注意如果返回值为-1，就说明索引越界了；
    * indexOf（int ch，int，fromIndex）：这个方法就是说从指定位置往后找返回字符在该字符串中第一次出现处的索引，比如“woaizhongguo”indexOf（'o',2）那返回值就是6而不是1，也不是11；
    * indexOf（Sting str）：这个方法基本就类似前面的了，只不过它是在参数里给一个子字符串，然后返回该子字符串在该字符串中第一次出现处的索引，比如"woaixuexi"要查"ai"这个子字符串在整个字符串中出现的索引位置那返回值就是2
    * indexOf（String str，int fromIndex）这个方法不在累述
    * lastIndexOf（int ch）：这个方法也是跟indexof相反，它是从后往前找返回字符在字符串中最后一次出现处的索引，也就是说找索引的时候是倒着找的但是返回值还是按照正的索引顺序返回的比如"woaiwo"用lastindexof查找‘w’返回的值是4而不是1
    * lastIndexOf(int ch,fromindex)

```java
    public String longestCommonPrefix(String[] strs) {
        if(strs.length == 0)
            return "";
        String prefix = strs[0];
        for(int i = 0; i < strs.length; i++) {
            while(strs[i].indexOf(prefix) != 0) {//前缀肯定是最短的，所以如果有这个相同前缀的话，肯定在每个string里面都有
                prefix = prefix.substring(0, prefix.length() - 1);//前缀就从第一个字符串进行截取就可以了
                if(prefix.isEmpty())
                    return "";
            }
        }
        return prefix;
    }
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

## 数字计算

* pow(x, n)
    * n%2==0 -> x^n = x^(n/2) * x^(n/2) = (x*x)^(n/2)
    * n%2==1 -> x^n = x*(x^(n/2) * x^(n/2)) = x * (x*x)^(n/2)

```java
public double pow(double x, int n) {
            if(n == 0)
                return 1;
            if(n == Integer.MIN_VALUE){//-2147483648不能直接换成正的，会溢出
                return myPow(x*x, n/2);
            }
            if(n < 0){
                x = 1/x;
                n = -n;
            }
            if(n%2 == 1) 
                return myPow(x*x, n/2)*x;
            else
                return myPow(x*x, n/2);
                }
```

* sqrt(x)

使用二分查找到最接近的平方根

    * I have seen many variants using Binary Search, the key difference is the search range. It seems easy to do it but actually there are some traps we need to take care. I made this just for a note for me.
      Search range summary:
      
      * [1, Integer.MAX_VALUE](easy but not recommend)
      * [1, x](recommended)
      * [1, x/2](you need to do math to prove it)
    * For case 2 and case 3, we need to take care of the corner case by making sure right >= left for [left, right], so:
      2. x >= 1 for [1, x] => so we need to take care of the corner case: x < 1
      3. x/2 >= 1 for [1, x/2]=> x >= 2 => so we need to take care of the corner case: x < 2
   
      
```java
class Solution {
    public int mySqrt(int x) {
        long l=0,r=x; //in case of overflow
        while(l<r){
            long mid=l+(r-l)/2+1;
            if(mid*mid>x) r=mid-1;
            else l=mid;
        }
        return (int)l;
    }
}
```

* String number add

```java
class Solution {
    public String addStrings(String num1, String num2) {
        //num1 < num2
        if(num1.length()>num2.length()){
            String temp = num1;
            num1 = num2;
            num2 = temp;
        }
        int l1 = num1.length();
        int l2 = num2.length();
        char[] res = new char[l2+1];
        int carry = 0;
        for(int i=0;i<l1;i++){
            res[l2-i] = (char)(carry + num1.charAt(l1-i-1)+num2.charAt(l2-i-1)-'0');
            if(res[l2-i]>'9'){
                carry=1;
                res[l2-i]-=10;
            }else{
                carry=0;
            }
            
        }
        for(int i=l1;i<l2;i++){
            res[l2-i] = (char)(carry + num2.charAt(l2-i-1));
            if(res[l2-i]>'9'){
                carry=1;
                res[l2-i]-=10;                
            }else{
                carry=0;
            }
        }
        res[0] = (char)(carry+'0');
        String result = String.valueOf(res);
        if(res[0]=='0') return result.substring(1);
        else return result;
    }
}
```

* 计算加减式
    * [basic-calculator](https://leetcode.com/problems/basic-calculator/)
  
```java
public int calculate(String s) {
        Stack<Integer> stack = new Stack<Integer>();
        int result = 0;
        int number = 0;
        int sign = 1;
        for(int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if(Character.isDigit(c)) {
                number = 10 * number + (int)(c  - '0');
            }
            else if(c == '+') {
                number = sign * number;
                result += number;
                number = 0;
                sign = 1;
            } else if(c == '-') {
                number = sign * number;
                result += number;
                number = 0;
                sign = -1;
            } else if( c == '(') {
                stack.push(result);
                stack.push(sign);
                result = 0;
                sign = 1;
            } else if( c == ')') {
                number = sign * number;
                result += number;
                number = 0; 
                result *= stack.pop();
                result += stack.pop();
            }
        }
        //最后以数字结尾的话
        if(number != 0) return result += sign * number;
        return result;
    }
```

* 加减乘除

```java
public int calculate(String s) {
        Stack<Integer> stack = new Stack<Integer>();
        int result = 0;
        int number = 0;
        char sign = '+';
        for(int i = 0; i < s.length(); i++ ) {
            
            char c = s.charAt(i);
            if(Character.isDigit(c)) {
                number = number * 10 + (int)(c - '0');
            }
            if((!Character.isDigit(c) && c != ' ') || i == s.length()-1) {
                if( sign == '+') {
                    stack.push(number);
                }
                if(sign == '-') {
                    stack.push(-number);
                }
                if(sign == '*') {
                    stack.push(stack.pop() * number);
                }
                if(sign == '/') {
                    stack.push(stack.pop() / number);
                }
                
                sign = c;
                number = 0;
            }
        }
        
        for(int i : stack) {
            result += i;
        }
        
        return result;
    }
```

* [Missing Num](https://leetcode.com/problems/missing-number/)
    * 在n长的数组中，包含0到n这n+1个数字中的n个。有个一数字[0, n]不在里面。
    * 将数组的索引[0, n-1]想成抽屉的编号，如果是n缺少的话，每个抽屉其实都能装到自己的数字，n就单独出来了。
    * 如果是[0, n-1]中的数字缺少了，说明n这个数字占了其中一个抽屉，那个数字（索引编号还贴在抽屉上）就单出来了。
    * 使用异或操作可以将单独的数字找出来。

```java
     public int missingNumber(int[] nums) {   
            
            int missing = nums.length;
            for(int i = 0; i < nums.length; i++) {
                missing ^= i ^ nums[i];
            }
            
            return missing;
        }
```

## Int处理

* Palindrome Number 回文数
    * 转换成String 使用reverse()判断是否相同
    * [reverse Integer](https://leetcode.com/problems/reverse-integer/)，再看是否相同，但是可能会溢出
    * 只转换后一半的数字，与前面一半的数字比较：1221 --> 12 12 12321 --> 12 123/10

```java
public boolean isPalindrome(int x) {
    if(x < 0 || (x % 10 == 0 && x != 0))
        return false;
    
    int rev = 0;
    while(x > rev) { //当前面的数字小于等于后面翻转的数字时，就到中间了
        rev = rev * 10 + x % 10;
        x /= 10;
    }
    
    return (x == rev || x == rev / 10);
}
```

## LinkedList

* Remove Linked List Elements

```java
public ListNode removeElements(ListNode head, int val) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        head = dummy;
        while(head.next != null) {
            if(head.next.val == val)
                head.next = head.next.next;
            else
                head = head.next;
        }
        return dummy.next;
    }
    
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        if(head == null)
            return null;
        ListNode res = removeElements(head.next, val);
        if(head.val == val) {
            return res;
        }else {
            head.next = res;
            return head;
        }
    }
}
```

* middle ListNode

```java
public ListNode middleNode(ListNode head) {
    ListNode slow = head, fast = head;
    while(fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    return slow;
}
```

* reverse List
    * 将链表翻转

```java
public ListNode reverse(ListNode head) {
    ListNode pre = null;
    while(head != null) {
        ListNode next = head.next;
        head.next = pre;
        pre = head;
        head = next;
    }
    return pre;
}
```

* 翻转部分List
    * [reverse list II](https://leetcode.com/problems/reverse-linked-list-ii/)

```java
public ListNode reverseBetween(ListNode head, int m, int n) {
        if(head == null)
            return null;
        
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        
        ListNode pre = dummy;
        for(int i = 0; i < m - 1; i++)
            pre = pre.next;//反转链表前面一个节点
        
        ListNode start = pre.next;//要反转链表的头一个节点
        ListNode then = start.next;//目前正要反转的节点
        
        // 1 - 2 -3 - 4 - 5 ; m=2; n =4 ---> pre = 1, start = 2, then = 3
        // dummy-> 1 -> 2 -> 3 -> 4 -> 5
        
        for(int i = 0; i < n - m; i++) {
            start.next = then.next;
            then.next = pre.next;
            pre.next = then;
            then = start.next;
        }
        
        // first reversing : dummy->1 - 3 - 2 - 4 - 5; pre = 1, start = 2, then = 4
        // second reversing: dummy->1 - 4 - 3 - 2 - 5; pre = 1, start = 2, then = 5 (finish)
        
        return dummy.next;
    }
```

* 有环的LinkedList
    * [Linked List CycleII](https://leetcode.com/problems/linked-list-cycle-ii/)
    * two pointers看解释
    * 延伸[287. Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)
    * 将数组当成链表的索引表
    * 鸽笼原理，一个数组[n+1]中有1-n个不相同的数，肯定有一个数字是重复的。如果把数组中的每个数当做是索引值的话，没有重复数字的情况下，就会形成一个没有环的链。如果中间有重复值的话，就一定会出现环。

```java
    public int findDuplicate(int[] nums) {
            int slow = nums[0];
            int fast = nums[0];
            
            do{
                slow = nums[slow];
                fast = nums[nums[fast]];
            }while(slow != fast);
            
            int head = nums[0];
            while(head != slow) {
                head = nums[head];
                slow = nums[slow];
            }
            
            return head;
        }
```

* [二进制加](https://leetcode.com/problems/add-binary/)
```java
    public String addBinary(String a, String b) {
            StringBuilder sb = new StringBuilder();
                    
            int carry = 0;
                    
            for(int i = a.length() - 1, j = b.length() - 1; i >= 0 || j >= 0; i--, j--) {
                int sum = carry;
                if(i >= 0)
                    sum += a.charAt(i) - '0';
                if(j >= 0)
                    sum += b.charAt(j) - '0';
                sb.append(sum % 2);
                carry = sum / 2;
            }
                    
            if(carry != 0)
                sb.append(carry);
            return sb.reverse().toString();
        }
```
    
## 指针

* 将数组中的数当成指针
    * [442. Find All Duplicates in an Array](https://leetcode.com/problems/find-all-duplicates-in-an-array)

```java
public List<Integer> findDuplicates(int[] nums) {
        List<Integer> res = new ArrayList<>();
        
        for (int i = 0; i < nums.length; ++i) {
            int index = Math.abs(nums[i])-1;//要去绝对值 索引
            if (nums[index] < 0)//是负的说明，之前遇到过
                res.add(Math.abs(index+1));
            nums[index] = -nums[index];
        }
        
        return res;
    }
```
   * 448. Find All Numbers Disappeared in an Array

```java
    public List<Integer> findDisappearedNumbers(int[] nums) {
        
        List<Integer> res = new ArrayList<>();
        for(int i = 0; i < nums.length; i++) {
            int val = Math.abs(nums[i]) - 1;
            if(nums[val] > 0) {
                nums[val] = - nums[val];
            }
        }
        for(int i = 0; i < nums.length; i++) {
            if(nums[i] > 0 ) {
                res.add(i+1);
            }
        }
        return res;
    }
    
    //使用普通的额外的数据结构
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> res = new ArrayList<>();
        
        int[] seen = new int[nums.length];
        
        for(int i = 0; i < nums.length; i++) {
            seen[nums[i] - 1]++;
        }
        
        for(int i = 0; i < seen.length; i++) {
            if(seen[i] >= 2)
                res.add(i + 1);
        }
        
        return res;
    }
```
## 表达式校验

* [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)
    * 常规做法：用stack保存左括号，遇到右括号检验栈顶是不是对应的左括号，是的话就pop，不是就不正确
    * 非常规，思想相同：

```java
    public boolean isValid(String s) {
            Stack<Character> stack = new Stack<Character>();
    	for (char c : s.toCharArray()) {
    		if (c == '(')
    			stack.push(')');
    		else if (c == '{')
    			stack.push('}');
    		else if (c == '[')
    			stack.push(']');
    		else if (stack.isEmpty() || stack.pop() != c)
    			return false;
    	}
    	return stack.isEmpty();
```

## 三分查找
* [guess number](https://leetcode.com/problems/guess-number-higher-or-lower/solution/)

```java
    public int guessNumber(int n) {
            int low = 1;
            int high = n;
            while (low <= high) {
                int mid1 = low + (high - low) / 3;
                int mid2 = high - (high - low) / 3;
                int res1 = guess(mid1);
                int res2 = guess(mid2);
                if (res1 == 0)
                    return mid1;
                if (res2 == 0)
                    return mid2;
                else if (res1 < 0)
                    high = mid1 - 1;
                else if (res2 > 0)
                    low = mid2 + 1;
                else {
                    low = mid1 + 1;
                    high = mid2 - 1;
                }
            }
            return -1;
        }
```