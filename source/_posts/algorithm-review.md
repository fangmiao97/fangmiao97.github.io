---
title: 1月算法复习
date: 2020/01/19
categories:
    - 算法
tags:
    - LeetCode
---

## Path Sum 3

* 二叉树中任意起点和终点上的路径和等于sum

使用前缀和，每次把一个路径看成一个数组，计算前缀和，两个点的差值为sum时即找到。

```java
    public int pathSum(TreeNode root, int sum) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);  //Default sum = 0 has one count，一个节点的val刚好等于sum也是答案
        return backtrack(root, 0, sum, map); 
    }
    //BackTrack one pass
    public int backtrack(TreeNode root, int sum, int target, Map<Integer, Integer> map){
        if(root == null)
            return 0;
        sum += root.val;
        int res = map.getOrDefault(sum - target, 0);//See if there is a subarray sum equals to target
        map.put(sum, map.getOrDefault(sum, 0)+1);
        //Extend to left and right child
        res += backtrack(root.left, sum, target, map) + backtrack(root.right, sum, target, map);
        map.put(sum, map.get(sum)-1);   //Remove the current node so it wont affect other path
        return res;
    }
```

最后一定要注意有一个将map中sum对应值减一的操作

## Binary Tree Maximum Path Sum

找到二叉树里面具有最大和（max sum）的路径，这个路径不一定要从上到下，就是二叉树中随便一条路径，但是不能有分叉，也就是说路径是一条，不会分开。

```java
class Solution {
    //全局最大值，最终返回的值
    int max = Integer.MIN_VALUE;
    
    public int maxPathSum(TreeNode root) {
        helper(root);
        return max;
    }
    
    //返回包括这个root在内的最大路径，3中选择：root本身，root+left的值，root+right的值
    public int helper(TreeNode root) {
        if(root == null)
            return 0;
        
        //默认都会有返回值，最小值为0
        int left = Math.max(0, helper(root.left));
        int right = Math.max(0, helper(root.right));
        
        //更新max
        max = Math.max(max, root.val + left + right);
        return root.val+Math.max(left,right);//只能返回一边，而且包括root
    }
}
```

## Sum Root to Leaf Numbers

![](https://pic.downk.cc/item/5e245b2d2fb38b8c3c7c8b76.jpg)

```java
class Solution {
    
    int res = 0;
    
    public int sumNumbers(TreeNode root) {
        helper(root, 0);
        return res;
    }
    
    public void helper(TreeNode root, int temp) {
        if(root == null) {
            return;
        }
        if(root.left == null && root.right == null) {
            temp = temp * 10 + root.val;
            res += temp;
        }
        
        helper(root.left, temp * 10 + root.val);
        helper(root.right, temp * 10 + root.val);
    }
}
```

## Compares two strings lexicographically字典序比较两String

```java
    //相等时返回0，String < anotherString 一个负数（第一个差值）, 
    // String > anotherString 正数
    //String是用char array保存的    
    public int compareTo(String anotherString) {
        int len1 = value.length;
        int len2 = anotherString.value.length;
        int lim = Math.min(len1, len2);
        char v1[] = value;
        char v2[] = anotherString.value;

        int k = 0;
        while (k < lim) {//从前往后比较
            char c1 = v1[k];
            char c2 = v2[k];
            if (c1 != c2) {//不相等就比较
                return c1 - c2;//返回的是差值
            }
            k++;
        }
        return len1 - len2;
    }
```
## [Smallest String Starting From Leaf](https://leetcode.com/problems/smallest-string-starting-from-leaf/)

```java
class Solution {
    String ans = "~";//注意这个
    public String smallestFromLeaf(TreeNode root) {
        dfs(root, new StringBuilder());
        return ans;
    }
    
    public void dfs(TreeNode root, StringBuilder sb) {
        if(root == null)
            return;
        
        if(root.left == null && root.right == null) {
            sb.insert(0, (char)('a' + root.val));
            String s = sb.toString();
            if (s.compareTo(ans) < 0)
                ans = s;
            sb.deleteCharAt(0);
        }
        
        sb.insert(0, (char)('a' + root.val));
        dfs(root.left, sb);
        dfs(root.right, sb);
        sb.deleteCharAt(0);
    }
}
```
## [Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths/)

![](https://pic.downk.cc/item/5e253e3a2fb38b8c3c955904.jpg)

使用String和StringBuilder的区别
"StringBuilder" is a mutable object, it will hold its value after returning.Whereas String creates a copy in every recursion, you don't need to worry about the "side-effect" when backtrack.


```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        //dfs(res, "", root);
        helper(res, new StringBuilder(), root);
        return res;
    }
    
    //使用String，因为这里都是符号应用生成新的String（immutable）
    public void dfs(List<String> res, String temp, TreeNode root) {
        if(root == null)
            return;
        
        if(root.left == null && root.right == null) {
            res.add(temp + root.val);
        }
        dfs(res, temp+root.val+"->", root.left);
        dfs(res, temp+root.val+"->", root.right);
    }
    
    //使用StringBuilder的话，操作的都是同一个StringBuilder，
    // 所以要注意怎么恢复成原来的状态
    public void helper(List<String> res, StringBuilder temp, TreeNode root) {
        if(root == null)
            return;
       
        //记录原来的长度 
        int len = temp.length();
        temp.append(root.val);
        if(root.left == null && root.right == null) {
            res.add(temp.toString());
        }
        temp.append("->");
        helper(res, temp, root.left);
        helper(res, temp, root.right);
        temp.setLength(len);//截取成原来的长度，再返回
    }
}
```

## Delete Node in a BST

```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
    if(root == null){
        return null;
    }
    if(key < root.val){
        root.left = deleteNode(root.left, key);
    }else if(key > root.val){
        root.right = deleteNode(root.right, key);
    }else{
        if(root.left == null){
            return root.right;
        }else if(root.right == null){
            return root.left;
        }
        
        TreeNode minNode = findMin(root.right);
        root.val = minNode.val;
        root.right = deleteNode(root.right, root.val);
    }
    return root;
}

private TreeNode findMin(TreeNode node){
    while(node.left != null){
        node = node.left;
    }
    return node;
}
}
```

## [138. Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)

使用map先保存新创建的node，再连接节点。

```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/
class Solution {
    public Node copyRandomList(Node head) {
        if(head == null)
            return null;
        
        Map<Node, Node> map = new HashMap<>();
        
        Node node = head;
        while(node != null) {
            map.put(node, new Node(node.val));
            node = node.next;
        }
        
        node = head;
        while(node != null) {
            //注意这两句！！！！！！
            map.get(node).next = map.get(node.next);
            map.get(node).random = map.get(node.random);
            node = node.next;
        }

        return map.get(head);
    }
}
```

法二，不使用map

![](https://pic.downk.cc/item/5e2d30bb2fb38b8c3c8fbc9f.jpg)

```java
class Solution {
    public Node copyRandomList(Node head) {
        if(head == null)
            return null;
        Node node = head;
        while(node != null) {
            Node next = node.next;
            node.next = new Node(node.val);
            node.next.next = next;
            node = next;
        }
        
        node = head;
        while(node != null) {
            if(node.random != null) {
                node.next.random = node.random.next;
            }
            node = node.next.next;
        }
        
        node = head;
        Node copyHead = head.next;
        Node copy = copyHead;
        while(copy.next != null) {
            node.next = node.next.next;
            node = node.next;
            
            copy.next = copy.next.next;
            copy = copy.next;
        }
        node.next =node.next.next;
        
        return copyHead;
    }
}
```

## [图clone](https://leetcode.com/problems/clone-graph/)

* dfs
```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> neighbors;
    
    public Node() {
        val = 0;
        neighbors = new ArrayList<Node>();
    }
    
    public Node(int _val) {
        val = _val;
        neighbors = new ArrayList<Node>();
    }
    
    public Node(int _val, ArrayList<Node> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
}
*/
class Solution {
    
    private Map<Node, Node> map = new HashMap<>();
    public Node cloneGraph(Node node) {
        return dfsClone(node);
    }
    
    Node dfsClone(Node node) {
        if(node == null)
            return null;
        
        if(map.containsKey(node))
            return map.get(node);
        
        Node newNode = new Node(node.val);
        map.put(node, newNode);
        for(Node neighbor: node.neighbors) {
            newNode.neighbors.add(dfsClone(neighbor));
        }
        return newNode;
    }
}
```

* bfs

```java
class Solution {
    public Node cloneGraph(Node node) {
        if(node == null)
            return  null;
        
        Queue<Node> queue = new LinkedList<>();
        queue.add(node);
        
        Map<Node, Node> map = new HashMap<>();
        map.put(node, new Node(node.val));
        
        while(!queue.isEmpty()) {
            Node curr = queue.poll();
            for(Node neighbor: curr.neighbors) {
                if(!map.containsKey(neighbor)) {
                    map.put(neighbor, new Node(neighbor.val));
                    queue.add(neighbor);
                }
                map.get(curr).neighbors.add(map.get(neighbor));
            }
        }
        return map.get(node);
    }
}
```






