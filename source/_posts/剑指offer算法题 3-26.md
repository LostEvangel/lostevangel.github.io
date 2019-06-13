---
title: 剑指offer算法题 3——26
date: 2019-3-21 21:55:35
tags: [刷题,面试]
categories: [面试,算法题]
description: 搞定剑指offer
---

# 剑指offer算法题 3——26

## 03 二维数组中的查找
在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。


```java
//从右上角开始遍历。
public class Solution {
    public boolean Find(int target, int [][] array) {
        int row = array.length;
        int col = array[0].length;
        if(row==0 || col==0)
            return false;
        int i = 0, j = col - 1;
        while(array[i][j]!=target){
            if(array[i][j]<target){
                i++;
            }else{
                j--;
            }
            if(i > row - 1 || j < 0){
                return false;
            }
        }
        return true;
    }
}
```

## 04 替换空格
请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

```java
public class Solution {
    public String replaceSpace(StringBuffer str) {
        String res = "";
        for(int i=str.length()-1;i>=0;i--){
            if(str.charAt(i)==' ')
                res = "%20" + res;
            else
                res = str.charAt(i) + res;
        }
        return res;
    }
}
```

## 05 从尾到头打印链表
输入一个链表，按链表值从尾到头的顺序返回一个ArrayList。


```java
// 用栈 或 反转链表

/**
*    public class ListNode {
*        int val;
*        ListNode next = null;
*
*        ListNode(int val) {
*            this.val = val;
*        }
*    }
*
*/
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        ArrayList<Integer> res = new ArrayList<Integer>();
        ListNode newHead = null;
        ListNode left = null;
        ListNode now = listNode;
        while(now != null){
            left = newHead;
            newHead = now;
            now = now.next;
            newHead.next = left;
        }
        while(newHead != null){
            res.add(newHead.val);
            newHead = newHead.next;
        }
        return res;
    }
}
```

## 06 重建二叉树
输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

```java
/**
 * Definition for binary tree
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
import java.util.Arrays;
public class Solution {
    public TreeNode reConstructBinaryTree(int [] pre,int [] in) {
        if(pre.length == 0){
            return null;
        }
        TreeNode root = new TreeNode(pre[0]);
        int mid = 0;
        for(int i = 0;i < in.length;i++){
            if(in[i] == pre[0])
                mid = i;
        }
        root.left = reConstructBinaryTree(Arrays.copyOfRange(pre, 1, 1 + mid),
                                         Arrays.copyOfRange(in, 0, mid));
        root.right = reConstructBinaryTree(Arrays.copyOfRange(pre, 1 + mid, pre.length),
                                         Arrays.copyOfRange(in, mid + 1, in.length));
        return root;
    }
}
```

## 07 两个栈实现队列

```java
import java.util.Stack;

public class Solution {
    Stack<Integer> stack1 = new Stack<Integer>();
    Stack<Integer> stack2 = new Stack<Integer>();
    
    public void push(int node) {
        stack1.push(node);
    }
    
    public int pop() {
        if(stack2.isEmpty()){
            while(!stack1.isEmpty()){
                int num = stack1.pop();
                stack2.push(num);
            }
        }
        return stack2.pop();

    }
}
```

## 08 旋转数组的最小数字
把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

```
import java.util.ArrayList;
public class Solution {
    public int minNumberInRotateArray(int [] array) {
        if(array.length == 0)
            return 0;
        int low = 0;
        int mid = (array.length + low) / 2;
        int high = array.length - 1;
        while(low < high){
            mid = low + (high - low) / 2; 
            if(array[mid] > array[high]){
                low = mid + 1;
            }else if (array[mid] < array[high]){
                high = mid;
            }else{
                high--;
            }
        }
        return array[low];
    }
}
```
## 09 斐波那契数列
大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0）。
n<=39

```java
// 使用动态规划
public class Solution {
    public int Fibonacci(int n) {
        int f1 = 0;
        int f2 = 1;
        int index = 0;
        int res = 0;
        while(index <= n){
            if(index == 0){
                res = f1;
            }else if(index == 1){
                res = f2;
            }
            else{
                res = f1 + f2;
                f1 = f2;
                f2 = res;
            }
            index ++;
        }
        return res;
    }
}
```

## 10 二进制中1的个数
输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。

```java
// 自己写的
public class Solution {
    public int NumberOf1(int n) {
        int count = 0;
        for(int i = 0; i < 32; i++){
            if((n & 1) == 1){
                count++;
            }
            n = n >> 1;
        }
        return count;
    }
}
// 网上更简练的，n按位与n-1，可以将最后一个1变成0
public class Solution {
    public int NumberOf1(int n) {
        int count = 0;
        while(n!=0){
            count += 1;
            n &= (n-1);
        }
        return count;
    }
}
```

## 11 数值的整数次方
给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。

```java
//  & 1 判断奇偶
public class Solution {
    public double Power(double base, int exponent) {
        if(base == 0)
            return 0;
        if(exponent == 0)
            return 1;
        if(exponent < 0)
            return 1 / Power(base, exponent * (-1));
        if((exponent & 1) == 1){
            return base * Power(base, exponent / 2) * Power(base, exponent / 2);
        }else{
            return Power(base, exponent / 2) * Power(base, exponent / 2);
        }
  }
}
```
## 14 调整数组顺序使奇数位于偶数前面
输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

```java
// 不开空间，用指针也比较麻烦，时间复杂度也不低，没找到指针比较好的办法
import java.util.List;
import java.util.ArrayList;
public class Solution {
    public void reOrderArray(int [] array) {
        ArrayList<Integer> list1 = new  ArrayList<Integer>();
        ArrayList<Integer> list2 = new  ArrayList<Integer>();
        for(int i = 0; i < array.length; i++){
            if((array[i] & 1) == 1){
                list1.add(array[i]);
            }else{
                list2.add(array[i]);
            }
        }
        List<Integer> listAll = new ArrayList<Integer>();
        listAll.addAll(list1);
        listAll.addAll(list2);
        for(int i = 0; i < array.length; i++){
            array[i] = listAll.get(i);
        }
    }
}
```

## 15 输入一个链表，输出该链表中倒数第k个结点
链表中倒数第k个结点

```
if(right == null)
    return null;
```
<font color=#ff0000 >注意这句的位置，要放在right = right.next;前面，否则{1,2,3,4,5},5/6 会有问题。</font>
```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
        if(k == 0 || head == null)
            return null;
        int index = 0;
        ListNode right = head;
        for(int i = 0;i < k; i++){
            if(right == null)
                return null;
            right = right.next;
        }
        ListNode left = head;
        while(right != null){
            right = right.next;
            left = left.next;
        }
        return left;
    }
}
```

## 16 反转链表
输入一个链表，反转链表后，输出新链表的表头。

<font color=#ff0000 >要注意 now = now.next; newHead.next = left;的先后顺序</font>

```java
// 要注意 now = now.next; newHead.next = left;的先后顺序
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode ReverseList(ListNode head) {
        if(head == null || head.next == null){
            return head;
        }
        ListNode newHead = null;
        ListNode left = null;
        ListNode now = head;
        while(now != null){
            left = newHead;
            newHead = now;
            now = now.next;
            newHead.next = left;
        }
        return newHead;
    }
}
```

## 17 合并两个排序的链表
输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
// 非递归
public class Solution {
    public ListNode Merge(ListNode list1,ListNode list2) {
        if(list1 == null)
            return list2;
        if(list2 == null)
            return list1;
        ListNode head = new ListNode(-1);
        ListNode now = head;
        while(list1 != null && list2 != null){
            if(list1.val < list2.val){
                now.next = list1;
                list1 = list1.next;
            }
            else{
                now.next = list2;
                list2 = list2.next;
            }
            now = now.next;
        }
        if(list1 != null)
            now.next = list1;
        if(list2 != null)
            now.next = list2;
        return head.next;
    }
}
// 递归
public class Solution {
    public ListNode Merge(ListNode list1,ListNode list2) {
        if(list1 == null)
            return list2;
        if(list2 == null)
            return list1;
        ListNode node = list1;
        if(list1.val < list2.val){
            node = list1;
            node.next = Merge(list1.next, list2);
        }else{
            node = list2;
            node.next = Merge(list1, list2.next);            
        }
        return node;
    }
}
```

## 18 树的子结构
输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

```java
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    public boolean HasSubtree(TreeNode root1,TreeNode root2) {
        if(root2 == null || root1 == null)
            return false;
        return isSubtree(root1, root2) 
            || HasSubtree(root1.left, root2) 
            || HasSubtree(root1.right, root2);
    }
    private boolean isSubtree(TreeNode root1, TreeNode root2) {
        if(root2 == null){
            return true;
        }
        if(root1 == null)
            return false;
        if(root1.val == root2.val){
            return isSubtree(root1.left, root2.left) && isSubtree(root1.right, root2.right);
        }else{
            return false;
        }
    }
}
```

## 19 二叉树的镜像
操作给定的二叉树，将其变换为源二叉树的镜像。

```java
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    public void Mirror(TreeNode root) {
        if(root == null)
            return;
        TreeNode node = root.left;
        root.left = root.right;
        root.right = node;
        Mirror(root.left);
        Mirror(root.right);
    }
}
```


## 20 顺时针打印矩阵
输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.


```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printMatrix(int [][] matrix) {
       if(matrix.length == 0 || matrix[0].length == 0)
           return null;
        int row = matrix.length, col = matrix[0].length;
        int top = 0, left = 0, right = col - 1, buttom = row - 1;
        ArrayList<Integer> res = new ArrayList<Integer>();
        while(left <= right && top <= buttom){
            for(int i = left; i <= right; i++){
                res.add(matrix[top][i]);
            }
            for(int i = top + 1; i <= buttom; i++){
                res.add(matrix[i][right]);
            }
            if(top != buttom)
                for(int i = right - 1; i >= left; i--){
                    res.add(matrix[buttom][i]);
                }
            if(left != right)
                for(int i = buttom - 1; i > top; i--){
                    res.add(matrix[i][left]);
                }
            left++;top++;right--;buttom--;
        }
        return res;
    }
}
```

## 21 包含min函数的栈
定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。

```java
import java.util.Stack;

public class Solution {
    Stack<Integer> stack = new Stack<>();
    Stack<Integer> minStack = new Stack<>();
    
    public void push(int node) {
        stack.push(node);
        int min = node;
        if(!minStack.isEmpty())
            min = minStack.pop();
        minStack.push(min);
        if(node < min)
            min = node;
        minStack.push(min);
    }
    
    public void pop() {
        stack.pop();
        minStack.pop();
    }
    
    public int top() {
        int num = stack.pop();
        stack.push(num);
        return num;
    }
    
    public int min() {
        int num = minStack.pop();
        minStack.push(num);
        return num;
    }
}
```

## 22 栈的压入、弹出序列
输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

```java
import java.util.ArrayList;
import java.util.Stack;

public class Solution {
    public boolean IsPopOrder(int [] pushA,int [] popA) {
        if(pushA.length != popA.length || 
               pushA.length == 0 ||
               popA.length == 0)
            return false;
        Stack<Integer> stack = new Stack<Integer>();
        int index = 0;
        for(int i = 0; i < pushA.length; i++){
            stack.push(pushA[i]);
            while(stack.peek() == popA[index]){
                stack.pop();
                index++;
                if(index == popA.length)
                    return true;
            }
        }
        return false;
    }
}
```

## 23 从上往下打出二叉树
从上往下打印出二叉树的每个节点，同层节点从左至右打印。

```java
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
        ArrayList<Integer> res = new ArrayList<Integer>();
        if(root == null)
            return res;
        Queue<TreeNode> queue = new LinkedList<TreeNode>();
        queue.offer(root);
        while(!queue.isEmpty()){
            int size = queue.size();
            for(int i = 0; i < size; i++){
                TreeNode node = queue.poll();
                res.add(node.val);
                if(node.left != null)
                    queue.offer(node.left);
                if(node.right != null)
                    queue.offer(node.right);
            }
        }
        return res;
    }
}
```

## 24 二叉搜索树的后序遍历
输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

```java
public class Solution {
    public boolean VerifySquenceOfBST(int [] sequence) {
        if(sequence.length == 0)
            return false;
        return judge(sequence, 0, sequence.length -1 );
    }
    private boolean judge(int[] sequence, int start ,int end){
        int root = end;
        int i = start;
        if(start >= end)
            return true;
        while(i < end && sequence[i] < sequence[end])
            i++;
        for(int j = i; j < end; j++){
            if(sequence[j] < sequence[end])
                return false;
        }
        return judge(sequence, start, i - 1 ) && judge(sequence, i, end - 1);
    }
}
```

## 25 二叉树中和为某一值的路径
输入一颗二叉树的跟节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)

```java
import java.util.ArrayList;
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    ArrayList<ArrayList<Integer>> res = new ArrayList<ArrayList<Integer>>();
    public ArrayList<ArrayList<Integer>> FindPath(TreeNode root,int target) {
        ArrayList<Integer> array = new ArrayList<Integer>();
        if(target > 0 && root != null)
            find(root, target, array);
        return res;

    }
    private void find(TreeNode root,int target, ArrayList<Integer> array){
        array.add(root.val);
        target = target - root.val;
        if(target == 0 && root.left == null && root.right == null)
            res.add((ArrayList<Integer>)array.clone());
        else {
            if(root.left != null)
                find(root.left, target, array);
            if(root.right != null)
                find(root.right, target, array);
        }
        array.remove(array.size() - 1);
    }
}
```

## 26 复杂链表的复制
输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的head。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

<font color=#ff0000 >“输出结果中请不要返回参数中的节点引用”，在第三步时，就要RandomListNode newNode。</font>

```java
/*
public class RandomListNode {
    int label;
    RandomListNode next = null;
    RandomListNode random = null;

    RandomListNode(int label) {
        this.label = label;
    }
}
*/
public class Solution {
    public RandomListNode Clone(RandomListNode pHead)
    {
        if(pHead == null)
            return null;
        RandomListNode node = pHead;
        while(node != null){
            RandomListNode newNode = new RandomListNode(node.label);
            newNode.next = node.next;
            node.next = newNode;
            node = node.next.next;
        }
        node = pHead;
        while(node != null){
            if(node.random != null)
                node.next.random = node.random.next;
            node = node.next.next;
        }
        node = pHead;
        RandomListNode newHead = node.next;
        while(node != null){
            RandomListNode newNode = node.next;
            node.next = node.next.next;
            newNode.next = newNode.next == null? null: newNode.next.next;
            node = node.next;
        }
        return newHead;
    }
}
```



