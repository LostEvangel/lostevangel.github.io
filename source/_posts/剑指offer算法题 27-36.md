---
title: 剑指offer算法题 27——36
date: 2019-3-23 20:03:31
tags: [刷题,面试]
categories: [面试,算法题]

---

# 剑指offer算法题 27——36

# 剑指offer算法题 27

## 27 二叉搜索树转换成双向链表
输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

<font color='red'>“不能创建任何新的结点”，但是需要new tail new tail
</font>

递归或栈的方法中序遍历。

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
    TreeNode head = null;
    TreeNode tail = null;
    public TreeNode Convert(TreeNode pRootOfTree) {
        if(pRootOfTree == null)
            return null;
        ConvertNode(pRootOfTree);
        return head;
    }
    private void ConvertNode(TreeNode node){
        if(node == null)
            return;
        ConvertNode(node.left);
        if(head == null){
            head = node;
            tail = node;
        }else{
            tail.right = node;
            node.left = tail;
            tail = node;
        }
        ConvertNode(node.right);
        
    }
}
```

## 28 字符串排列

输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。
- 按字典序打印，需要把结果：Collections.sort(res);
- 避免重复的，需要判断：if(i != index && chars[i] == chars[index])

```java
import java.util.ArrayList;
import java.util.Collections;
public class Solution {
    ArrayList<String> res = new ArrayList<String>();
    public ArrayList<String> Permutation(String str) {
        char[] chars = str.toCharArray();
        if(str.length() == 0)
            return res;
        res = Permutation(chars, 0);
        Collections.sort(res);
        return res;
    }
    private ArrayList<String> Permutation(char[] chars, int index){
        if(index == chars.length - 1)
            res.add(String.valueOf(chars));
        else{
            for(int i = index; i < chars.length; i++){
                if(i != index && chars[i] == chars[index]){
                    continue;
                }
                swap(chars, i, index);
                Permutation(chars, index + 1);
                swap(chars, index, i);

            }
        }
        return res;
    }
    private void swap(char[] chars, int i ,int j){
        char  temp = chars[i];
        chars[i] = chars[j];
        chars[j] = temp;
    }
}
```
## 29 数组中次数超过的数

数组中有一个数字出现的次数超过的数数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

- <font color='red'>可以用hashmap</font>
- <font color='red'>可以先排序</font>
- <font color='red'>超过一半，即超过其他数之和。用一个num记录当前count大于零的数，count表示此数的次数，如果新遍历的数不是num，count--，否则count++，当count归零，更新num。</font>

```java
public class Solution {
    public int MoreThanHalfNum_Solution(int [] array) {
        if(array.length == 0)
            return 0;
        int num = array[0], count = 1;
        for(int i = 1; i < array.length; i++){
            if(array[i] != num){
                if(count == 0)
                    num = array[i];
                else
                    count--;
            }else
                count++;
        }
        if(count > 0)
            return num;
        else
            return 0;
    }
}
```

## 30 最小的K个数
输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

```java
import java.util.PriorityQueue;
import java.util.ArrayList;
import java.util.Comparator;
public class Solution {
    public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
        ArrayList<Integer> res = new ArrayList<Integer>();
        if(k > input.length || k == 0)
            return res;
        PriorityQueue<Integer> queue = new PriorityQueue<Integer>(k, new Comparator<Integer>() {
 
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2.compareTo(o1);
            }
        });
        for(int i = 0; i < input.length; i++){
            if(queue.size() < k){
                queue.offer(input[i]);
            }else if(queue.peek() > input[i]){
                    queue.poll();
                    queue.offer(input[i]);
            }
        }
        while(!queue.isEmpty()){
            res.add(queue.poll());
        }
        return res;
    }
}
```

## 31 连续子数组的最大和
HZ偶尔会拿些专业问题来忽悠那些非计算机专业的同学。今天测试组开完会后,他又发话了:在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？(子向量的长度至少是1)

```
public class Solution {
    public int FindGreatestSumOfSubArray(int[] array) {
        int cur = 0;
        int max = Integer.MIN_VALUE;
        for(int i = 0; i < array.length; i++){
            if(cur > 0)
                cur += array[i];
            else
                cur = array[i];
            if(cur > max)
                max = cur;
        }
        return max;
    }
}
```
## 32 整数中1出现的次
求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。

<font color='red'>规律：当计算右数第 i 位包含的 x 的个数时</font>

<font color='red'>1. 取第 i位左边(高位)的数字，乘以 10^(i-1)，得到基础值 a</font>

<font color='red'>2. 取第 i 位数字，计算修正值</font>

<font color='red'>3. 如果大于 x , 则结果为 a + 10^(i-1)</font>

<font color='red'>4. 如果小于 x，则结果为 a</font>

<font color='red'>5. 如果等于 x，则取第 i 位右边(低位)数字，设为 b，最后结果为 a + b + 1</font>



```
// n - n / i * i 是当前左边一位
public class Solution {
    public int NumberOf1Between1AndN_Solution(int n) {
        int count = 0;
        for(int i = 1; n / i != 0; i *= 10){
            int base = n / i / 10 ;
            base *= i;
            int bias = n / i % 10;
            if(bias < 1)
                count += base;
            else if(bias > 1)
                count += base + i;
            else{
                count += base + n - n / i * i + 1;
            }
            
        }
        return count;
    }
}
```

## 33 把数组排成最小的数
输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

<font color='red'>类似冒泡排序</font>

```
public class Solution {
    public String PrintMinNumber(int [] numbers) {
        ArrayList<String> list = new ArrayList<>();
        for(int n : numbers){
            list.add(String.valueOf(n));
        }
        for(int i = 0;i <list.size(); i++){
            for(int j = 0; j < list.size() - 1 - i; j++){
                if(Long.parseLong(list.get(j) + list.get(j + 1)) > Long.parseLong(list.get(j + 1) + list.get(j))) {
                    String temp = list.get(j + 1);
                    list.set(j + 1 , list.get(j));
                    list.set(j, temp);
                }
            }
        }
        String res = "";
        for (String s:list) {
            res += s;
        }
        return res;
    }
}
```

## 34 丑数
把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

```
import java.util.PriorityQueue;
public class Solution {
    public int GetUglyNumber_Solution(int index) {
        if(index == 0)
            return 0;
        PriorityQueue<Long> queue = new PriorityQueue();
        queue.offer(1L);
        long num = 1L;
        for(int i = 0; i != index; i++){
            num = queue.poll();
            while(!queue.isEmpty() && num == queue.peek())
                queue.poll();
            queue.offer(num * 2);
            queue.offer(num * 3);
            queue.offer(num * 5);
        }
        return (int)num;
    }
}
```

# 35第一个只出现一次的字符

在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.


```
import java.util.HashMap;
public class Solution {
    public int FirstNotRepeatingChar(String str) {
        char[] cs = str.toCharArray();
        HashMap<Character, Integer> map = new HashMap<>();
        for (char c : cs) {
            if (!map.containsKey(c))
                map.put(c, 1);
            else
                map.put(c, map.get(c) + 1);
        }
        for (int i = 0; i < cs.length; i++) {
            if (map.get(cs[i]) == 1)
                return i;
        }
        return -1;
    }
}
```

## 36 数组中的逆序对
在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007

<font color='red'>归并排序，注意是从后往前放copy数组。</font>

```
public class Solution {
    public int InversePairs(int [] array) {
        if(array.length == 0)
            return 0;
        return mergeSort(array, 0, array.length - 1);
    }
    private int mergeSort(int[] array, int start, int end){
        int mid = (end + start)/ 2;
        if(end == start)
            return 0;
        int leftCount = mergeSort(array, start, mid);
        int rightCount = mergeSort(array, mid + 1, end);
        int i = mid, j = end;
        int count = 0;
        int index = end - start;
        int[] copy = new int[end - start + 1];
        while(i >= start && j >= mid + 1){
            if(array[i] > array[j]){
                count = (count + j - mid) % 1000000007;
                copy[index--] = array[i--];
            }else{
                copy[index--] = array[j--];
            }
        }
        while(i >= start){
            copy[index--] = array[i--];
        }
        while(j >= mid + 1){
            copy[index--] = array[j--];
        }
        for(int k = 0; k < copy.length; k++)
            array[start + k] = copy[k];
        return (leftCount + rightCount + count) % 1000000007;
    }
}
```
