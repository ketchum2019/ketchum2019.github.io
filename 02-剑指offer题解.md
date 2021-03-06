# 剑指offer题解

![image-20200728193111655](https://raw.githubusercontent.com/ketchum2019/ketchum2019.github.io/master/images/排序.png)

## 68. 最低公共祖先

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q)
        return root;
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    return left == null ? right : right == null ? left : root;
}
```

## 67. 把字符串转换成整数 ##

> 题目：将一个字符串转换成一个整数(实现Integer.valueOf(string)的功能，但是string不符合数字要求时返回0)，要求不能使用字符串转换整数的库函数。 数值为0或者字符串不是一个合法的数值则返回0。

```java
public class Solution {
    public int StrToInt(String str) {
        if (str == null || str.length() == 0) {
            return 0;
        }
        int num = 0, index = 0;
        boolean minus = false;
        if (str.charAt(0) == '+') {
            index++;
        } else if (str.charAt(0) == '-') {
            minus = true;
            index++;
        }
        while (index < str.length()) {
            char digit = str.charAt(index++);
            if (digit >= '0' && digit <= '9') {
                num = num * 10 + digit - '0';
            } else {
                return 0;
            }
        }
        return minus ? -num : num;
    }
}
```

## 66. 构建乘积数组

> 题目：给定一个数组A[0,1,...,n-1]，请构建一个数组B[0,1,...,n-1]，其中B中的元素B[i]=A[0]×A[1]×...×A[i-1]×A[i+1]×...×A[n-1]。不能使用除法。

```java
public class Solution {
    public int[] multiply(int[] A) {
        if (A == null || A.length < 2) {
            return A;
        }
        int[] B = new int[A.length];
        B[0] = 1;
        for (int i = 1; i < A.length; i++) {
            B[i] = B[i - 1] * A[i - 1];
        }
        for (int i = A.length - 2, temp = 1; i >= 0; i--) {
            temp *= A[i + 1];
            B[i] *= temp;
        }
        return B;
    }
}
```

## 65. 不用加减乘除做加法

> 写一个函数，求两个整数之和，要求在函数体内不得使用+、-、*、/四则运算符号。

```java
public class Solution {
    public int Add(int num1,int num2) {
        int sum = num1;
        while(num2 !=0){
            sum = num1 ^ num2;
            num2 = (num1 & num2) << 1;
            num1 = sum;
        }
        return sum;
    }
}
```

## 64. 求 1+2+3+...+n

> 要求不能使用乘除法、for、while、if、else、switch、case 等关键字及条件判断语句 A ? B : C。

使用递归解法最重要的是指定返回条件，但是本题无法直接使用 if 语句来指定返回条件。

条件与 && 具有短路原则，即在第一个条件语句为 false 的情况下不会去执行第二个条件语句。利用这一特性，将递归的返回条件取非然后作为 && 的第一个条件语句，递归的主体转换为第二个条件语句，那么当递归的返回条件为 true 的情况下就不会执行递归的主体部分，递归返回。

本题的递归返回条件为 n <= 0，取非后就是 n > 0；递归的主体部分为 sum += Sum_Solution(n - 1)，转换为条件语句后就是 (sum += Sum_Solution(n - 1)) > 0。

```
public int Sum_Solution(int n) {
    int sum = n;
    boolean b = (n > 0) && ((sum += Sum_Solution(n - 1)) > 0);
    return sum;
}
```

## 62. 圆圈中最后剩下的数字 ##

>  题目：0,1,···,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字。求出这个圆圈里剩下的最后一个数字。

```java
//环形链表模拟圆圈
import java.util.LinkedList;

public class Solution {
    public int LastRemaining_Solution(int n, int m) {
        if (n < 1 || m < 1) {
            return -1;
        }
        LinkedList<Integer> numbers = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            numbers.add(i);
        }
        int current = 0;
        while (numbers.size() > 1) {
            current = (current + m - 1) % numbers.size();
            numbers.remove(current);
        }
        return numbers.getFirst();
    }
}
```

```java
//递推公式：f(n,m) = [f(n-1,m)+m]%n，n>1
public class Solution {
    public int LastRemaining_Solution(int n, int m) {
        if (n < 1 || m < 1) {
            return -1;
        }
        int last = 0;
        for (int i = 2; i <= n; i++) {
            last = (last + m) % i;
        }
        return last;
    }
}
```

## 61. 扑克牌中的顺子 ##

> 题目：从扑克牌中随机抽5张牌，判断是不是一个顺子，即这5张牌是不是连续的。2~10为数字本身，A为1，J为11，Q为12，K为13，而大、小王可以看成任意数字。为了方便起见，你可以认为大小王是0。
>
> https://www.nowcoder.com/practice/762836f4d43d43ca9deb273b3de8e1f4

```java
import java.util.Arrays;

public class Solution {
    public boolean isContinuous(int[] numbers) {
        if (numbers == null || numbers.length != 5) {
            return false;
        }
        Arrays.sort(numbers);
        int zero = 0;
        // 统计数组中0的个数
        for (int i = 0; i < numbers.length && numbers[i] == 0; i++) {
            zero++;
        }
        int gap = 0;
        // 统计数组中的间隔数目
        for (int i = zero + 1; i < numbers.length; i++) {
            // 两个数相等，有对子，不可能是顺子
            if (numbers[i - 1] == numbers[i]) {
                return false;
            }
            gap += numbers[i] - numbers[i - 1] - 1;
        }
        return gap <= zero;
    }
}
```

## 60. n个骰子的点数 ##

> 题目：把n个骰子扔在地上，所有骰子朝上一面的点数之和为s。输入n，打印出s的所有可能的值出现的概率。

```

```



## 59. 队列的最大值 ##

> 题目一：滑动窗口的最大值。
>
> 给定一个数组和滑动窗口的大小，找出所有滑动窗口里数值的最大值。例如，如果输入数组{2,3,4,2,6,2,5,1}及滑动窗口的大小3，那么一共存在6个滑动窗口，他们的最大值分别为{4,4,6,6,6,5}； 针对数组{2,3,4,2,6,2,5,1}的滑动窗口有以下6个： {[2,3,4],2,6,2,5,1}， {2,[3,4,2],6,2,5,1}， {2,3,[4,2,6],2,5,1}， {2,3,4,[2,6,2],5,1}， {2,3,4,2,[6,2,5],1}， {2,3,4,2,6,[2,5,1]}。

```java
import java.util.ArrayList;
import java.util.LinkedList;

public class Solution {
    public ArrayList<Integer> maxInWindows(int[] num, int size) {
        ArrayList<Integer> result = new ArrayList<>();
        if (num == null || num.length == 0 || size < 1 || size > num.length) {
            return result;
        }
        LinkedList<Integer> list = new LinkedList<>();
        for (int i = 0; i < size; i++) {
            while (!list.isEmpty() && num[list.getLast()] <= num[i]) {
                list.removeLast();
            }
            list.add(i);
        }
        for (int i = size; i < num.length; i++) {
            result.add(num[list.getFirst()]);
            while (!list.isEmpty() && num[list.getLast()] <= num[i]) {
                list.removeLast();
            }
            if (!list.isEmpty() && i - list.getFirst() >= size) {
                list.removeFirst();
            }
            list.add(i);
        }
        result.add(num[list.getFirst()]);
        return result;
    }
}
```

## 58. 翻转字符串

> 题目一：翻转单词顺序。
>
> 输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student."，则输出"student. a am I"

```java
public class Solution {
    public String ReverseSentence(String str) {
        if(str ==null ||str.length() ==0) return str;
        String[] str1 = str.split(" ");
        if (str1==null ||str1.length ==0) return str;
        StringBuilder s = new StringBuilder();
        for(int i = str1.length-1; i>=0; i--){
            if(i != 0){
                s.append(str1[i]).append(' ');
            }else{
                s.append(str1[i]);
            }

        }
        return s.toString();
    }
}
```

```java
//解法二
public class Solution {
    public String ReverseSentence(String str) {
        if (str == null || str.length() == 0) {
            return str;
        }
        char[] data = str.toCharArray();
        // 翻转整个句子
        reverse(data, 0, data.length - 1);
        // 翻转句子中的每个单词
        int start = 0, end = 0;
        while (start < data.length) {
            if (data[start] == ' ') {
                start++;
                end++;
            } else if (end == data.length || data[end] == ' ') {
                reverse(data, start, end - 1);
                start = ++end;
            } else {
                end++;
            }
        }
        return new String(data);
    }
    
    private void reverse(char[] data, int start, int end) {
        while (start < end) {
            char temp = data[start];
            data[start] = data[end];
            data[end] = temp;
            start++;
            end--;
        }
    }
}
```



> 题目二：左旋转字符串
>
> 字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。

```java
public class Solution {
    public String LeftRotateString(String str, int n) {
        if (str == null || str.length() == 0 || n < 1 || n >= str.length()) {
            return str;
        }
        char[] data = str.toCharArray();
        // 翻转字符串的前面n个字符
        reverse(data, 0, n - 1);
        // 翻转字符串的后面部分
        reverse(data, n, data.length - 1);
        // 翻转整个字符串
        reverse(data, 0, data.length - 1);
        return new String(data);
    }
    
    private void reverse(char[] data, int start, int end) {
        while (start < end) {
            char temp = data[start];
            data[start] = data[end];
            data[end] = temp;
            start++;
            end--;
        }
    }
}
```

## 57. 和为s的数字

> 题目一：和为s的两个数字。
>
> 输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，则输出任意一对即可

## 56. 数组中数字出现的次数 ##

> 题目一：数组中只出现一次的两个数字。
>
> 一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。

两个不相等的元素在位级表示上必定会有一位存在不同，将数组的所有元素异或得到的结果为不存在重复的两个元素异或的结果。

diff &= -diff 得到出 diff 最右侧不为 0 的位，也就是不存在重复的两个元素在位级表示上最右侧不同的那一位，利用这一位就可以将两个元素区分开来。

```java
public void FindNumsAppearOnce(int[] nums, int num1[], int num2[]) {
    int diff = 0;
    for (int num : nums)
        diff ^= num;
    diff &= -diff;
    for (int num : nums) {
        if ((num & diff) == 0)
            num1[0] ^= num;
        else
            num2[0] ^= num;
    }
}
```

## 55. 二叉树的深度

> 题目一：二叉树的深度
>
> 输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

```java
public class Solution {
    public int TreeDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        return Math.max(TreeDepth(root.left), TreeDepth(root.right)) + 1;
    }
}
```

> 题目二：平衡二叉树
>
> 输入一棵二叉树，判断该二叉树是否是平衡二叉树。如果某二叉树中任意节点的左、右子树的深度相差不超过1，那么它就是一棵平衡二叉树。

```java
public class Solution {
    private int mDepth;
    
    public boolean IsBalanced_Solution(TreeNode root) {
        if (root == null) {
            mDepth = 0;
            return true;
        }
        if (!IsBalanced_Solution(root.left)) {
            return false;
        }
        int left = mDepth;
        if (!IsBalanced_Solution(root.right)) {
            return false;
        }
        int right = mDepth;
        int diff = left - right;
        if (diff <= 1 && diff >= -1) {
            mDepth = 1 + (left > right ? left : right);
            return true;
        } else {
            return false;
        }
    }
}
```

## 54. 二叉搜索树的第k大节点

> 题目：给定一棵二叉搜索树，请找出其中的第k小的结点。例如，（5，3，7，2，4，6，8）中，按结点数值大小顺序第三小结点的值为4。
>
> [https://www.nowcoder.com/practice/ef068f602dde4d28aab2b210e859150a]

中序遍历可以找出二叉搜索树的第k大节点

```java
private TreeNode ret;
private int cnt = 0;

public TreeNode KthNode(TreeNode pRoot, int k) {
    inOrder(pRoot, k);
    return ret;
}

private void inOrder(TreeNode root, int k) {
    if (root == null || cnt >= k)
        return;
    inOrder(root.left, k);
    cnt++;
    if (cnt == k)
        ret = root;
    inOrder(root.right, k);
}
```

## 53. 在排序数组中查找数字

> 题目一：数字在排序数组中出现的次数。
>
> 统计一个数字在排序数组中出现的次数。例如，输入排序数组{1, 2, 3, 3, 3, 3, 4, 5}和数字3，由于3在这个数组中出现了4次，因此输出4。

```java
public int GetNumberOfK(int[] nums, int K) {
    int first = binarySearch(nums, K);
    int last = binarySearch(nums, K + 1);
    return (first == nums.length || nums[first] != K) ? 0 : last - first;
}

private int binarySearch(int[] nums, int K) {
    int l = 0, h = nums.length;
    while (l < h) {
        int m = l + (h - l) / 2;
        if (nums[m] >= K)
            h = m;
        else
            l = m + 1;
    }
    return l;
}
```

> 题目二：0~n-1中缺失的数字
>
> 一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0 ~ n-1之内。在范围0 ~ n-1内的n个数字中有且只有一个数字不在该数组中，请找出这个数字
>
> 解题思路：二分查找：找到第一个元素和下标不等的数字

```java
public int getMissingNumber(int[] numbers) {
    if (numbers == null || numbers.length == 0) {
        return -1;
    }
    int left = 0, right = numbers.length - 1;
    while (left <= right) {
        int mid = (left + right) >> 1;
        if (numbers[mid] != mid) {
            if (mid == 0 || numbers[mid - 1] == mid - 1) {
                return mid;
            }
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    if (left == numbers.length) {
        return left;
    }
    // 无效的输入，比如数组不是按要求排序的，
    // 或者有数字不在0~n-1范围之内
    return -1;
}
```

## 52. 两个链表的第一个公共节点

```java
public class Solution {
    public ListNode FindFirstCommonNode(ListNode pHead1, ListNode pHead2) {
        if (pHead1 == null || pHead2 == null) {
            return null;
        }
        ListNode p1 = pHead1, p2 = pHead2;
        // 得到两个链表的长度
        int len1 = 0, len2 = 0;
        while (p1 != null) {
            len1++;
            p1 = p1.next;
        }
        while (p2 != null) {
            len2++;
            p2 = p2.next;
        }
        int delta = len2 - len1;
        p1 = pHead1;
        p2 = pHead2;
        // 先在长链表上走几步，再同时在两个链表上遍历
        if (delta < 0) {
            while (delta != 0) {
                p1 = p1.next;
                delta++;
            }
        } else {
            while (delta != 0) {
                p2 = p2.next;
                delta--;
            }
        }
        while (p1 != p2) {
            p1 = p1.next;
            p2 = p2.next;
        }
        // 得到第一个公共节点
        return p1;
    }
}
```

## 51. 数组中的逆序对

> 题目：在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出，即输出P%1000000007。例如，在数组{7, 5, 6, 4}中，一共存在5个逆序对，分别是(7, 6)、(7, 5)、(7, 4)、(6, 4)和(5, 4)。
>
> 解题思路：归并排序

```java
public class Solution {
    public int InversePairs(int[] array) {
        if (array == null || array.length == 0) {
            return 0;
        }
        int[] copy = new int[array.length];
        for (int i = 0; i < array.length; i++) {
            copy[i] = array[i];
        }
        return inverse(array, copy, 0, array.length - 1);
    }
    
    private int inverse(int[] array, int[] copy, int start, int end) {
        if (start == end) {
            return 0;
        }
        int mid = (start + end) / 2;
        int left = inverse(copy, array, start, mid);
        int right = inverse(copy, array, mid + 1, end);
        int leftIndex = mid, rightIndex = end, copyIndex = end, count = 0;
        while (leftIndex >= start && rightIndex > mid) {
            if (array[leftIndex] > array[rightIndex]) {
                copy[copyIndex--] = array[leftIndex--];
                count += rightIndex - mid;
                if (count >= 1000000007) {
                    count %= 1000000007;
                }
            } else {
                copy[copyIndex--] = array[rightIndex--];
            }
        }
        while (leftIndex >= start) {
            copy[copyIndex--] = array[leftIndex--];
        }
        while (rightIndex > mid) {
            copy[copyIndex--] = array[rightIndex--];
        }
        return (left + right + count) % 1000000007;
    }
}
```

## 50. 第一个只出现一次的字符

> 题目一：字符串中第一个只出现一次的字符。
>
> 在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）。

解决方案：哈希表

```java
public class Solution {
    public int FirstNotRepeatingChar(String str) {
        if (str == null || str.length() == 0) {
            return -1;
        }
        int[] counts = new int[256];
        for (int i = 0; i < str.length(); i++) {
            char c = str.charAt(i);
            counts[c]++;
        }
        for (int i = 0; i < str.length(); i++) {
            if (counts[str.charAt(i)] == 1) {
                return i;
            }
        }
        return -1;
    }
}
```

## 49. 丑数

> 题目：把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

```java
public class Solution {
    public int GetUglyNumber_Solution(int index) {
        if (index <= 0) {
            return 0;
        }
        int[] ugly = new int[index];
        ugly[0] = 1;
        for (int i = 1, multiply2 = 0, multiply3 = 0, multiply5 = 0; i < index; i++) {
            ugly[i] = Math.min(ugly[multiply2] * 2, Math.min(ugly[multiply3] * 3, ugly[multiply5] * 5));
            while (ugly[multiply2] * 2 <= ugly[i]) {
                multiply2++;
            }
            while (ugly[multiply3] * 3 <= ugly[i]) {
                multiply3++;
            }
            while (ugly[multiply5] * 5 <= ugly[i]) {
                multiply5++;
            }
        }
        return ugly[index - 1];
    }
}
```

## 48. 最长不含重复字符的子字符串

> 题目：请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长子字符串的长度。假设字符串中只包含'a'~'z'的字符。例如，在字符串"arabcacfr"中，最长的不含重复字符的子字符串是"acfr"，长度为4。

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> dic = new HashMap<>();
        int res = 0, tmp = 0;
        for(int j = 0; j < s.length(); j++) {
            int i = dic.getOrDefault(s.charAt(j), -1); // 获取索引 i
            dic.put(s.charAt(j), j); // 更新哈希表
            tmp = tmp < j - i ? tmp + 1 : j - i; // dp[j - 1] -> dp[j]
            res = Math.max(res, tmp); // max(dp[j - 1], dp[j])
        }
        return res;
    }
}
```

## 47. 礼物的最大价值

> 题目：在一个m×n的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格，直到到达棋盘的右下角。给定一个棋盘及其上面的礼物，请计算你最多能拿到多少价值的礼物？

```java
class Solution {
    public int maxValue(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(i == 0 && j == 0) continue;
                if(i == 0) grid[i][j] += grid[i][j - 1] ;
                else if(j == 0) grid[i][j] += grid[i - 1][j];
                else grid[i][j] += Math.max(grid[i][j - 1], grid[i - 1][j]);
            }
        }
        return grid[m - 1][n - 1];
    }
}
```

## 46. 把数字翻译成字符串

> 题目：给定一个数字，我们按照如下规则把它翻译为字符串：0翻译成“a”，1翻译成“b”，……，11翻译成“l”，……，25翻译成“z”。一个数字可能有多个翻译。例如，12258有5种不同的翻译，分别是“bccfi”、“bwfi”、“bczi”、“mcfi”和“mzi”。请编程实现一个函数，用来计算一个数字有多少种不同的翻译方法。
>
> https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/solution/mian-shi-ti-46-ba-shu-zi-fan-yi-cheng-zi-fu-chua-6/

```java
class Solution {
    public int translateNum(int num) {
        String s = String.valueOf(num);
        int a = 1, b = 1;
        for(int i = 2; i <= s.length(); i++) {
            String tmp = s.substring(i - 2, i);
            int c = tmp.compareTo("10") >= 0 && tmp.compareTo("25") <= 0 ? a + b : a;
            b = a;
            a = c;
        }
        return a;
    }
}
```

```java
class Solution {
    public int translateNum(int num) {
        int a = 1, b = 1, x, y = num % 10;
        while(num != 0) {
            num /= 10;
            x = num % 10;
            int tmp = 10 * x + y;
            int c = (tmp >= 10 && tmp <= 25) ? a + b : a;
            b = a;
            a = c;
            y = x;
        }
        return a;
    }
}
```

## 45. 把数组排成最小的数

> 题目：输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。
>
> https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/solution/mian-shi-ti-45-ba-shu-zu-pai-cheng-zui-xiao-de-s-4/

```java
class Solution {
    public String minNumber(int[] nums) {
        String[] strs = new String[nums.length];
        for(int i = 0; i < nums.length; i++) 
            strs[i] = String.valueOf(nums[i]);
        Arrays.sort(strs, (x, y) -> (x + y).compareTo(y + x));
        StringBuilder res = new StringBuilder();
        for(String s : strs)
            res.append(s);
        return res.toString();
    }
}
```

## 44. 数字序列中某一位的数字

> 题目：数字以0123456789101112131415···的格式序列化到一个字符序列中。在这个序列中，第5位（从0开始计数）是5，第13位是1，第19位是4，等等。请写一个函数，求任意第n位对应的数字。
>
> https://leetcode-cn.com/problems/shu-zi-xu-lie-zhong-mou-yi-wei-de-shu-zi-lcof/solution/mian-shi-ti-44-shu-zi-xu-lie-zhong-mou-yi-wei-de-6/

```java
class Solution {
    public int findNthDigit(int n) {
        int digit = 1;
        long start = 1;
        long count = 9;
        while (n > count) { // 1.
            n -= count;
            digit += 1;
            start *= 10;
            count = digit * start * 9;
        }
        long num = start + (n - 1) / digit; // 2.
        return Long.toString(num).charAt((n - 1) % digit) - '0'; // 3.
    }
}
```

## 43. 1~n整数中1出现的次数（未理解）

> 题目：输入一个整数n，求1 ~ n这n个整数的十进制表示中1出现的次数。例如，输入12，1 ~ 12这些整数中包含1的数字有1、10、11和12，1一共出现了5次。
>
> https://leetcode.com/problems/number-of-digit-one/discuss/64381/4+-lines-O(log-n)-C++JavaPython

```java
public int NumberOf1Between1AndN_Solution(int n) {
    int cnt = 0;
    for (int m = 1; m <= n; m *= 10) {
        int a = n / m, b = n % m;
        cnt += (a + 8) / 10 * m + (a % 10 == 1 ? b + 1 : 0);
    }
    return cnt;
}
```

## 42. 连续子数组的最大和

> 题目：输入一个整型数组，数组里有正数也有负数。数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。要求时间复杂度为O(n)。
>
> 例如，输入的数组为{1, -2, 3, 10, -4, 7, 2, -5}，和最大的子数组为{3, 10, -4, 7, 2}，因此输出为该子数组的和18。

```java
public class Solution {
    public int FindGreatestSumOfSubArray(int[] array) {
        if (array == null || array.length == 0) {
            return 0;
        }
        int sum = 0, max = Integer.MIN_VALUE;
        for (int num : array) {
            if (sum <= 0) {
                sum = num;
            } else {
                sum += num;
            }
            if (sum > max) {
                max = sum;
            }
        }
        return max;
    }
}
```

## 41. 数据流中的中位数

> 题目：如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。我们使用Insert()方法读取数据流，使用GetMedian()方法获取当前读取数据的中位数。

```java
/* 大顶堆，存储左半边元素 */
private PriorityQueue<Integer> left = new PriorityQueue<>((o1, o2) -> o2 - o1);
/* 小顶堆，存储右半边元素，并且右半边元素都大于左半边 */
private PriorityQueue<Integer> right = new PriorityQueue<>();
/* 当前数据流读入的元素个数 */
private int N = 0;

public void Insert(Integer val) {
    /* 插入要保证两个堆存于平衡状态 */
    if (N % 2 == 0) {
        /* N 为偶数的情况下插入到右半边。
         * 因为右半边元素都要大于左半边，但是新插入的元素不一定比左半边元素来的大，
         * 因此需要先将元素插入左半边，然后利用左半边为大顶堆的特点，取出堆顶元素即为最大元素，此时插入右半边 */
        left.add(val);
        right.add(left.poll());
    } else {
        right.add(val);
        left.add(right.poll());
    }
    N++;
}

public Double GetMedian() {
    if (N % 2 == 0)
        return (left.peek() + right.peek()) / 2.0;
    else
        return (double) right.peek();
}

```

## 41.2 字符流中第一个不重复的字符

```java
private int[] cnts = new int[256];
private Queue<Character> queue = new LinkedList<>();

public void Insert(char ch) {
    cnts[ch]++;
    queue.add(ch);
    while (!queue.isEmpty() && cnts[queue.peek()] > 1)
        queue.poll();
}

public char FirstAppearingOnce() {
    return queue.isEmpty() ? '#' : queue.peek();
}
```

## 40. 最小的K个数

> 题目：输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4。

应该使用大顶堆来维护最小堆，而不能直接创建一个小顶堆并设置一个大小，企图让小顶堆中的元素都是最小元素。

维护一个大小为 K 的最小堆过程如下：在添加一个元素之后，如果大顶堆的大小大于 K，那么需要将大顶堆的堆顶元素去除。

```java
public ArrayList<Integer> GetLeastNumbers_Solution(int[] nums, int k) {
    if (k > nums.length || k <= 0)
        return new ArrayList<>();
    PriorityQueue<Integer> maxHeap = new PriorityQueue<>((o1, o2) -> o2 - o1);
    for (int num : nums) {
        maxHeap.add(num);
        if (maxHeap.size() > k)
            maxHeap.poll();
    }
    return new ArrayList<>(maxHeap);
}
```

## 39. 数组中超过一半的数字

> 使用 cnt 来统计一个元素出现的次数，当遍历到的元素和统计元素相等时，令 cnt++，否则令 cnt--。如果前面查找了 i 个元素，且 cnt == 0，说明前 i 个元素没有 majority，或者有 majority，但是出现的次数少于 i / 2 ，因为如果多于 i / 2 的话 cnt 就一定不会为 0 。此时剩下的 n - i 个元素中，majority 的数目依然多于 (n - i) / 2，因此继续查找就能找出 majority。

```java
public int MoreThanHalfNum_Solution(int[] nums) {
    int majority = nums[0];
    for (int i = 1, cnt = 1; i < nums.length; i++) {
        cnt = nums[i] == majority ? cnt + 1 : cnt - 1;
        if (cnt == 0) {
            majority = nums[i];
            cnt = 1;
        }
    }
    int cnt = 0;
    for (int val : nums)
        if (val == majority)
            cnt++;
    return cnt > nums.length / 2 ? majority : 0;
}
```

## 38. 字符串的排列

> 题目：输入一个字符串，按字典序打印出该字符串中字符的所有排列。例如输入字符串abc，则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。

```java
import java.util.*;
public class Solution {
    ArrayList<String> res =new ArrayList<>();
    public ArrayList<String> Permutation(String str) {
        if(str == null ||str.length()==0) return res;
        char[] chars = str.toCharArray();
        backtracking(chars, new boolean[chars.length],new StringBuilder(""));
        return res;
    }
    public void backtracking(char[] chars, boolean[] hasused, StringBuilder s){
        if(s.length() == chars.length) res.add(new String(s));
        for(int i=0; i<chars.length; i++){
            if(hasused[i]) continue;
            if(i!=0 && chars[i]==chars[i-1] && !hasused[i-1]) continue;
            hasused[i]=true;
            s.append(chars[i]);
            backtracking(chars, hasused,s);
            s.deleteCharAt(s.length()-1);
            hasused[i]=false;
        }
    }
}
```

## 37. 序列化二叉树

>  题目：请实现两个函数，分别用来序列化和反序列化二叉树。可以根据前序遍历的顺序来序列化二叉树。在遍历二叉树碰到null时，这些null序列化为一个特殊字符'$'。另外，节点的数值之间要用一个特殊字符','隔开。

```java
private String deserializeStr;

public String Serialize(TreeNode root) {
    if (root == null)
        return "#";
    return root.val + " " + Serialize(root.left) + " " + Serialize(root.right);
}

public TreeNode Deserialize(String str) {
    deserializeStr = str;
    return Deserialize();
}

private TreeNode Deserialize() {
    if (deserializeStr.length() == 0)
        return null;
    int index = deserializeStr.indexOf(" ");
    String node = index == -1 ? deserializeStr : deserializeStr.substring(0, index);
    deserializeStr = index == -1 ? "" : deserializeStr.substring(index + 1);
    if (node.equals("#"))
        return null;
    int val = Integer.valueOf(node);
    TreeNode t = new TreeNode(val);
    t.left = Deserialize();
    t.right = Deserialize();
    return t;
}
```

## 36. 二叉搜索树和双向链表

> 题目：输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。
>
> 解决思路：中序遍历
>
> [https://cyc2018.github.io/CS-Notes/#/notes/36.%20%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E4%B8%8E%E5%8F%8C%E5%90%91%E9%93%BE%E8%A1%A8](https://cyc2018.github.io/CS-Notes/#/notes/36. 二叉搜索树与双向链表)

```java
private TreeNode pre = null;
private TreeNode head = null;

public TreeNode Convert(TreeNode root) {
    inOrder(root);
    return head;
}

private void inOrder(TreeNode node) {
    if (node == null)
        return;
    inOrder(node.left);
    node.left = pre;
    if (pre != null)
        pre.right = node;
    pre = node;
    if (head == null)
        head = node;
    inOrder(node.right);
}
```

## 35. 复杂链表的复制

> 题目：输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的head。
>
> 第一步，在每个节点的后面插入复制的节点。
>
> 第二步，对复制节点的 random 链接进行赋值。
>
> 第三步，拆分

```java
public RandomListNode Clone(RandomListNode pHead) {
    if (pHead == null)
        return null;
    // 插入新节点
    RandomListNode cur = pHead;
    while (cur != null) {
        RandomListNode clone = new RandomListNode(cur.label);
        clone.next = cur.next;
        cur.next = clone;
        cur = clone.next;
    }
    // 建立 random 链接
    cur = pHead;
    while (cur != null) {
        RandomListNode clone = cur.next;
        if (cur.random != null)
            clone.random = cur.random.next;
        cur = clone.next;
    }
    // 拆分
    cur = pHead;
    RandomListNode pCloneHead = pHead.next;
    while (cur.next != null) {
        RandomListNode next = cur.next;
        cur.next = next.next;
        cur = next;
    }
    return pCloneHead;
}

```

## 34. 二叉树中和为某一值的路径

> 题目：输入一颗二叉树的根节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。

```java
private ArrayList<ArrayList<Integer>> ret = new ArrayList<>();

public ArrayList<ArrayList<Integer>> FindPath(TreeNode root, int target) {
    backtracking(root, target, new ArrayList<>());
    return ret;
}

private void backtracking(TreeNode node, int target, ArrayList<Integer> path) {
    if (node == null)
        return;
    path.add(node.val);
    target -= node.val;
    if (target == 0 && node.left == null && node.right == null) {
        ret.add(new ArrayList<>(path));
    } else {
        backtracking(node.left, target, path);
        backtracking(node.right, target, path);
    }
    path.remove(path.size() - 1);
}

```

## 33. 二叉搜索树的后序遍历序列

> 题目：输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

```java
public boolean VerifySquenceOfBST(int[] sequence) {
    if (sequence == null || sequence.length == 0)
        return false;
    return verify(sequence, 0, sequence.length - 1);
}

private boolean verify(int[] sequence, int first, int last) {
    if (last - first <= 1)
        return true;
    int rootVal = sequence[last];
    int cutIndex = first;
    while (cutIndex < last && sequence[cutIndex] <= rootVal)
        cutIndex++;
    for (int i = cutIndex; i < last; i++)
        if (sequence[i] < rootVal)
            return false;
    return verify(sequence, first, cutIndex - 1) && verify(sequence, cutIndex, last - 1);
}
```

## 32.1 从上到下打印二叉树

> 题目一：不分行从上到下打印二叉树
>
> 从上往下打印出二叉树的每个节点，同层节点从左至右打印。

```java
public class Solution {
    public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
        ArrayList<Integer> result = new ArrayList<>();
        if (root == null) {
            return result;
        }
        LinkedList<TreeNode> nodeList = new LinkedList<>();
        nodeList.add(root);
        while (!nodeList.isEmpty()) {
            TreeNode node = nodeList.removeFirst();
            result.add(node.val);
            if (node.left != null) {
                nodeList.add(node.left);
            }
            if (node.right != null) {
                nodeList.add(node.right);
            }
        }
        return result;
    }
}
```

## 32.3 之字形打印二叉树

> 题目三：之字形打印二叉树
>
> 请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

```java
public ArrayList<ArrayList<Integer>> Print(TreeNode pRoot) {
    ArrayList<ArrayList<Integer>> ret = new ArrayList<>();
    Queue<TreeNode> queue = new LinkedList<>();
    queue.add(pRoot);
    boolean reverse = false;
    while (!queue.isEmpty()) {
        ArrayList<Integer> list = new ArrayList<>();
        int cnt = queue.size();
        while (cnt-- > 0) {
            TreeNode node = queue.poll();
            if (node == null)
                continue;
            list.add(node.val);
            queue.add(node.left);
            queue.add(node.right);
        }
        if (reverse)
            Collections.reverse(list);
        reverse = !reverse;
        if (list.size() != 0)
            ret.add(list);
    }
    return ret;
}
```

## 31. 栈的压入、弹出序列

> 题目：输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

```java
public boolean IsPopOrder(int[] pushSequence, int[] popSequence) {
    int n = pushSequence.length;
    Stack<Integer> stack = new Stack<>();
    for (int pushIndex = 0, popIndex = 0; pushIndex < n; pushIndex++) {
        stack.push(pushSequence[pushIndex]);
        while (popIndex < n && !stack.isEmpty() 
                && stack.peek() == popSequence[popIndex]) {
            stack.pop();
            popIndex++;
        }
    }
    return stack.isEmpty();
}
```

## 30. 包含min函数的栈

```java
private Stack<Integer> dataStack = new Stack<>();
private Stack<Integer> minStack = new Stack<>();

public void push(int node) {
    dataStack.push(node);
    minStack.push(minStack.isEmpty() ? node : Math.min(minStack.peek(), node));
}

public void pop() {
    dataStack.pop();
    minStack.pop();
}

public int top() {
    return dataStack.peek();
}

public int min() {
    return minStack.peek();
}
```

## 29. 顺时针打印矩阵

> 题目：输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

```java
public ArrayList<Integer> printMatrix(int[][] matrix) {
    ArrayList<Integer> ret = new ArrayList<>();
    int r1 = 0, r2 = matrix.length - 1, c1 = 0, c2 = matrix[0].length - 1;
    while (r1 <= r2 && c1 <= c2) {
        for (int i = c1; i <= c2; i++)
            ret.add(matrix[r1][i]);
        for (int i = r1 + 1; i <= r2; i++)
            ret.add(matrix[i][c2]);
        if (r1 != r2)
            for (int i = c2 - 1; i >= c1; i--)
                ret.add(matrix[r2][i]);
        if (c1 != c2)
            for (int i = r2 - 1; i > r1; i--)
                ret.add(matrix[i][c1]);
        r1++; r2--; c1++; c2--;
    }
    return ret;
}
```

## 28. 对称的二叉树

> 题目：请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

```java
boolean isSymmetrical(TreeNode pRoot) {
    if (pRoot == null)
        return true;
    return isSymmetrical(pRoot.left, pRoot.right);
}

boolean isSymmetrical(TreeNode t1, TreeNode t2) {
    if (t1 == null && t2 == null)
        return true;
    if (t1 == null || t2 == null)
        return false;
    if (t1.val != t2.val)
        return false;
    return isSymmetrical(t1.left, t2.right) && isSymmetrical(t1.right, t2.left);
}
```

## 27. 二叉树的镜像

> 题目：操作给定的二叉树，将其变换为源二叉树的镜像。

```java
public void Mirror(TreeNode root) {
    if (root == null)
        return;
    swap(root);
    Mirror(root.left);
    Mirror(root.right);
}
private void swap(TreeNode root) {
    TreeNode t = root.left;
    root.left = root.right;
    root.right = t;
}
```

## 26. 树的子结构

> 题目：输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

```java
public boolean HasSubtree(TreeNode root1, TreeNode root2) {
    if (root1 == null || root2 == null)
        return false;
    return isSubtreeWithRoot(root1, root2) || HasSubtree(root1.left, root2) || HasSubtree(root1.right, root2);
}

private boolean isSubtreeWithRoot(TreeNode root1, TreeNode root2) {
    if (root2 == null)
        return true;
    if (root1 == null)
        return false;
    if (root1.val != root2.val)
        return false;
    return isSubtreeWithRoot(root1.left, root2.left) &&
    	   isSubtreeWithRoot(root1.right, root2.right);
}

```

## 25. 合并两个排序列表

```java
public ListNode Merge(ListNode list1, ListNode list2) {
    if (list1 == null)
        return list2;
    if (list2 == null)
        return list1;
    if (list1.val <= list2.val) {
        list1.next = Merge(list1.next, list2);
        return list1;
    } else {
        list2.next = Merge(list1, list2.next);
        return list2;
    }
}
```

## 24. 反转链表

```java
public ListNode ReverseList(ListNode head) {
    ListNode newList = new ListNode(-1);
    while (head != null) {
        ListNode next = head.next;
        head.next = newList.next;
        newList.next = head;
        head = next;
    }
    return newList.next;
}
```

## 23. 链表中环的入口节点

```java
public ListNode EntryNodeOfLoop(ListNode pHead) {
    if (pHead == null || pHead.next == null)
        return null;
    ListNode slow = pHead, fast = pHead;
    do {
        fast = fast.next.next;
        slow = slow.next;
    } while (slow != fast);
    fast = pHead;
    while (slow != fast) {
        slow = slow.next;
        fast = fast.next;
    }
    return slow;
}
```



```java
public class Solution {

    public ListNode EntryNodeOfLoop(ListNode pHead) {
        if (pHead == null || pHead.next == null) {
            return null;
        }
        ListNode meetingNode = meetingNode(pHead);
        if (meetingNode == null) {
            return null;
        }
        // 得到环中节点的数目
        ListNode cur = meetingNode.next;
        int nodesInLoop = 1;
        while (cur != meetingNode) {
            nodesInLoop++;
            cur = cur.next;
        }
        ListNode behind = cur = pHead;
        // 先移动cur，次数为环中节点的数目
        while (nodesInLoop-- > 0) {
            cur = cur.next;
        }
        // 再移动behind和cur，相遇时即为入口节点
        while (behind != cur) {
            behind = behind.next;
            cur = cur.next;
        }
        return behind;
    }
    
    private ListNode meetingNode(ListNode pHead) {
        // 在链表中存在环时找到一快一慢两个指针相遇的节点，无环返回null
        ListNode cur = pHead.next.next, behind = pHead;
        while (cur != null) {
            if (cur == behind) {
                return cur;
            }
            if (cur.next != null) {
                cur = cur.next.next;
            } else {
                return null;
            }
            behind = behind.next;
        }
        return null;
    }
}
```

## 22. 链表中倒数第k个节点

```java
public class Solution {
    public ListNode FindKthToTail(ListNode head, int k) {
        if (head == null || k < 1) {
            return null;
        }
        ListNode cur = head;
        while (k-- > 1) {
            if (cur.next == null) {
                return null;
            }
            cur = cur.next;
        }
        ListNode behind = head;
        while (cur.next != null) {
            behind = behind.next;
            cur = cur.next;
        }
        return behind;
    }
}
```

## 21. 调整数组顺序使奇数位于偶数前面

> 题目：输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分。
>
> 扩展：输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，**并保证奇数和奇数，偶数和偶数之间的相对位置不变**。

```java
public class Solution {
    public void reOrderArray(int [] array) {
        int n = array.length;
        int[] aux = new int[n];
        int i = 0;
        for (int j = 0; j < n; j++) {
            if ((array[j] & 1) == 1) {
                if (i != j) {
                    array[i] = array[j];
                }
                i++;
            } else {
                aux[j - i] = array[j];
            }
        }
        for (int j = i; j < n; j++) {
            array[j] = aux[j - i];
        }
    }
}
```

## 20. 表示数值的字符串

```java
public class Solution {
    public boolean isNumeric(char[] str) {
        if (str == null || str.length == 0) {
            return false;
        }
        boolean hasDot = false, hasE = false;
        for (int i = 0; i < str.length; i++) {
            if (str[i] == '.') {
                if (hasDot) {
                    // 已经出现过'.'
                    return false;
                } else {
                    hasDot = true;
                }
            } else if (isE(str[i])) {
                if (hasE) {
                    // 已经出现过'E'
                    return false;
                }
                // 'E'后面不会再出现'E'和'.'
                hasE = true;
                hasDot = true;
            } else if (isPlusMinus(str[i])) {
                if (i != 0 && (!hasE || !isE(str[i - 1]))) {
                    // '+''-'不是第一个且前面不是'E'
                    return false;
                }
            } else if (!isNumber(str[i])) {
                return false;
            }
        }
        // 最后一个不是'E'
        return !isE(str[str.length - 1]);
    }
    
    private boolean isE(char c) {
        return c == 'E' || c == 'e';
    }
    
    private boolean isPlusMinus(char c) {
        return c == '+' || c == '-';
    }
    
    private boolean isNumber(char c) {
        return c >= '0' && c <= '9';
    }
}
```

使用正则表达式

```
[]  ： 字符集合
()  ： 分组
?   ： 重复 0 ~ 1 次
+   ： 重复 1 ~ n 次
*   ： 重复 0 ~ n 次
.   ： 任意字符
\\. ： 转义后的 .
\\d ： 数字
```

```java
public boolean isNumeric(char[] str) {
    if (str == null || str.length == 0)
        return false;
    return new String(str).matches("[+-]?\\d*(\\.\\d+)?([eE][+-]?\\d+)?");
}
```

## 19. 正则表达式匹配

> 题目：请实现一个函数用来匹配包括'.'和'*'的正则表达式。模式中的字符'.'表示任意一个字符，而'*'表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但是与"aa.a"和"ab*a"均不匹配

```java
public class Solution {
    public boolean match(char[] str, char[] pattern){
        if (str == null || pattern == null) {
            return false;
        }
        return match(str, pattern, 0, 0);
    }
    
    private boolean match(char[] str, char[] pattern, int strPos, int patternPos) {
        if (strPos == str.length && patternPos == pattern.length) {
            return true;
        } else if (patternPos == pattern.length) {
            return false;
        }
        if (patternPos + 1 < pattern.length && pattern[patternPos + 1] == '*') {
            if (strPos < str.length && (pattern[patternPos] == '.' || pattern[patternPos] == str[strPos])) {
                return match(str, pattern, strPos, patternPos + 2) // 匹配0个字符
                    || match(str, pattern, strPos + 1, patternPos + 2) // 匹配1个字符
                    || match(str, pattern, strPos + 1, patternPos); // 匹配多个字符
            } else {
                return match(str, pattern, strPos, patternPos + 2); // 匹配0个字符
            }
        }
        if (strPos < str.length && (pattern[patternPos] == '.' || pattern[patternPos] == str[strPos])) {
            return match(str, pattern, strPos + 1, patternPos + 1);
        }
        return false;
    }
}
```

## 18.1 删除链表的节点

> 题目一：在O(1)时间内删除链表节点。
> 给定单向链表的头指针和一个节点指针，定义一个函数在O(1)时间内删除该节点。

```java
public ListNode deleteNode(ListNode head, ListNode tobeDelete) {
    if (head == null || tobeDelete == null)
        return null;
    if (tobeDelete.next != null) {
        // 要删除的节点不是尾节点
        ListNode next = tobeDelete.next;
        tobeDelete.val = next.val;
        tobeDelete.next = next.next;
    } else {
        if (head == tobeDelete)
             // 只有一个节点
            head = null;
        else {
            ListNode cur = head;
            while (cur.next != tobeDelete)
                cur = cur.next;
            cur.next = null;
        }
    }
    return head;
}
```

## 18.2 删除链表中重复的节点。

> 在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5

```java
public ListNode deleteDuplication(ListNode pHead) {
    if (pHead == null || pHead.next == null)
        return pHead;
    ListNode next = pHead.next;
    if (pHead.val == next.val) {
        while (next != null && pHead.val == next.val)
            next = next.next;
        return deleteDuplication(next);
    } else {
        pHead.next = deleteDuplication(pHead.next);
        return pHead;
    }
}
```

```java
public class Solution {
    public ListNode deleteDuplication(ListNode pHead) {
        ListNode pre = null, node = pHead;
        while (node != null) {
            boolean needDelete = false;
            if (node.next != null && node.next.val == node.val) {
                needDelete = true;
            }
            if (needDelete) {
                int value = node.val;
                while (node != null && node.val == value) {
                    node = node.next;
                }
                if (pre == null) {
                    pHead = node;
                } else {
                    pre.next = node;
                }
            } else {
                pre = node;
                node = node.next;
            }
        }
        return pHead;
    }
}
```

## 17. 打印从1到最大的n位数

## 16. 数值的整数次方

> 题目：给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。

```java
public double Power(double base, int exponent) {
    if (exponent == 0)
        return 1;
    if (exponent == 1)
        return base;
    boolean isNegative = false;
    if (exponent < 0) {
        exponent = -exponent;
        isNegative = true;
    }
    double pow = Power(base * base, exponent / 2);
    if (exponent % 2 != 0)
        pow = pow * base;
    return isNegative ? 1 / pow : pow;
}
```

```java
public class Solution {
    public double Power(double base, int exponent) {
        if (base == 0) {
            return 0;
        }
        if (exponent == 0) {
            return 1;
        }
        boolean negative = exponent < 0;
        if (negative) {
            exponent = -exponent;
        }
        double result = base;
        while (--exponent > 0) {
            result *= base;
        }
        return negative ? 1 / result : result;
    }
}
```

时间复杂度：O(n)。空间复杂度：O(1)。

## 15. 二进制中1的个数

> 题目：输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。

```java
public class Solution {
    public int NumberOf1(int n) {
        int count = 0, flag = 1;
        while (flag > 0) {
            if ((n & flag) > 0) {
                count++;
            }
            flag <<= 1;
        }
        // 负数的最高位是1
        return n < 0 ? count + 1 : count;
    }
}
```

```java
public class Solution {
    public int NumberOf1(int n) {
        int count = 0;
        while (n != 0) {
            count++;
            // 把最右边的1变成0
            n = (n - 1) & n;
        }
        // 负数的最高位是1
        return n < 0 ? count + 1 : count;
    }
}
```

## 14. 剪绳子

> 题目：给你一根长度为n的绳子，请把绳子剪成m段（m、n都是整数，n>1并且m>1），每段绳子的长度记为k[0],k[1],···,k[m]。请问k[0]×k[1]×···×k[m]可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

```java
public int integerBreak(int n) {
    int[] dp = new int[n + 1];
    dp[1] = 1;
    for (int i = 2; i <= n; i++)
        for (int j = 1; j < i; j++)
            dp[i] = Math.max(dp[i], Math.max(j * (i - j), dp[j] * (i - j)));
    return dp[n];
}
```

## 13. 机器人的运动范围

> 题目：地上有一个m行和n列的方格。一个机器人从坐标0,0的格子开始移动，每一次只能向左，右，上，下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于k的格子。 例如，当k为18时，机器人能够进入方格（35,37），因为3+5+3+7 = 18。但是，它不能进入方格（35,38），因为3+5+3+8 = 19。请问该机器人能够达到多少个格子？

```java
public class Solution {
    public int movingCount(int threshold, int rows, int cols) {
        if (threshold < 1 || rows < 1 || cols < 1) {
            return 0;
        }
        boolean[][] visited = new boolean[rows][cols];
        return movingCount(threshold, rows, cols, 0, 0, visited);
    }
    
    private int movingCount(int threshold, int rows, int cols, int row, int col, boolean[][] visited) {
        int count = 0;
        if (row >= 0 && row < rows && col >= 0 && col < cols && !visited[row][col]
            && sum(row) + sum(col) <= threshold) {
            visited[row][col] = true;
            count = 1 + movingCount(threshold, rows, cols, row - 1, col, visited)
                + movingCount(threshold, rows, cols, row + 1, col, visited)
                + movingCount(threshold, rows, cols, row, col + 1, visited)
                + movingCount(threshold, rows, cols, row, col - 1, visited);
        }
        return count;
    }
    
    private int sum(int number) {
        int sum = 0;
        while (number > 0) {
            sum += number % 10;
            number /= 10;
        }
        return sum;
    }
}
```

## 12. 矩阵中的路径

> 题目：请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向左，向右，向上，向下移动一个格子。如果一条路径经过了矩阵中的某一个格子，则之后不能再次进入这个格子。 例如 a b c e s f c s a d e e 这样的3 X 4 矩阵中包含一条字符串"bcced"的路径，但是矩阵中不包含"abcb"路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入该格子。

```java
public class Solution {
    public boolean hasPath(char[] matrix, int rows, int cols, char[] str) {
        if (matrix == null || rows < 1 || cols < 1 || str == null) {
            return false;
        }
        boolean[][] visited = new boolean[rows][cols];
        for (int row = 0; row < rows; row++) {
            for (int col = 0; col < cols; col++) {
                if (hasPath(matrix, rows, cols, row, col, str, 0, visited)) {
                    return true;
                }
            }
        }
        return false;
    }
    
    private boolean hasPath(char[] matrix, int rows, int cols, int row, int col
                            , char[] str, int index, boolean[][] visited) {
        if (index == str.length) {
            return true;
        }
        if (row >= 0 && row < rows && col >= 0 && col < cols && !visited[row][col]
            && matrix[cols * row + col] == str[index]) {
            visited[row][col] = true;
            if (hasPath(matrix, rows, cols, row - 1, col, str, index + 1, visited)
               || hasPath(matrix, rows, cols, row + 1, col, str, index + 1, visited)
               || hasPath(matrix, rows, cols, row, col - 1, str, index + 1, visited)
               || hasPath(matrix, rows, cols, row, col + 1, str, index + 1, visited)) 
            {
                return true;
            }
            visited[row][col] = false;
        }
        return false;
    }
}
```

## 11.  旋转数组的最小数字

> 题目：把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

```java
import java.util.ArrayList;
public class Solution {
    public int minNumberInRotateArray(int [] array) {
        if (array == null || array.length == 0) {
            return 0;
        }
        int start = 0, end = array.length - 1, mid;
        while (array[start] >= array[end]) {
            if (start + 1 == end) {
                return array[end];
            }
            mid = (start + end) / 2;
            if (array[start] == array[mid] && array[end] == array[mid]) {
                return minNumberInOrder(array, start, end);
            }
            if (array[mid] >= array[start]) {
                start = mid;
            } else if (array[mid] <= array[end]) {
                end = mid;
            }
        }
        return array[0];
    }
    
    private int minNumberInOrder(int[] array, int start, int end) {
        int min = array[start];
        for (int i = start + 1; i <= end; i++) {
            if (array[i] < min) {
                min = array[i];
            }
        }
        return min;
    }
}
```

## 10.1 斐波那契数列

```java
public int Fibonacci(int n) {
    if (n <= 1)
        return n;
    int pre2 = 0, pre1 = 1;
    int fib = 0;
    for (int i = 2; i <= n; i++) {
        fib = pre2 + pre1;
        pre2 = pre1;
        pre1 = fib;
    }
    return fib;
}
```

## 10.2 青蛙跳台阶

```java
public int JumpFloor(int n) {
    if (n <= 2)
        return n;
    int pre2 = 1, pre1 = 2;
    int result = 0;
    for (int i = 2; i < n; i++) {
        result = pre2 + pre1;
        pre2 = pre1;
        pre1 = result;
    }
    return result;
}
```

## 10.3 变态跳台阶

> 一只青蛙一次可以跳上 1 级台阶，也可以跳上 2 级... 它也可以跳上 n 级。求该青蛙跳上一个 n 级的台阶总共有多少种跳法。

```java
public int JumpFloorII(int target) {
    return (int) Math.pow(2, target - 1);
}
```

```java
public int JumpFloorII(int target) {
    int[] dp = new int[target];
    Arrays.fill(dp, 1);
    for (int i = 1; i < target; i++)
        for (int j = 0; j < i; j++)
            dp[i] += dp[j];
    return dp[target - 1];
}
```

## 9.用两个栈实现队列

```java
Stack<Integer> in = new Stack<Integer>();
Stack<Integer> out = new Stack<Integer>();

public void push(int node) {
    in.push(node);
}

public int pop() throws Exception {
    if (out.isEmpty())
        while (!in.isEmpty())
            out.push(in.pop());

    if (out.isEmpty())
        throw new Exception("queue is empty");

    return out.pop();
}
```

## 8. 二叉树的下一个节点

> 给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回 。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。
>
> 解题思路：如果一个节点的右子树不为空，那么该节点的下一个节点是右子树的最左节点；否则，向上找第一个左链接指向的树包含该节点的祖先节点。

```java
public TreeLinkNode GetNext(TreeLinkNode pNode) {
    if (pNode.right != null) {
        TreeLinkNode node = pNode.right;
        while (node.left != null)
            node = node.left;
        return node;
    } else {
        while (pNode.next != null) {
            TreeLinkNode parent = pNode.next;
            if (parent.left == pNode)
                return parent;
            pNode = pNode.next;
        }
    }
    return null;
}
```

## 7. 重建二叉树

> 题目：输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回

```java
public class Solution {
    public TreeNode reConstructBinaryTree(int[] pre, int[] in) {
        if (pre == null || pre.length == 0 || in == null || in.length == 0 || pre.length != in.length) {
            return null;
        }
        return construct(pre, in, 0, pre.length - 1, 0, in.length - 1);
    }
    
    private TreeNode construct(int[] pre, int[] in, int preStart, int preEnd, int inStart, int inEnd) {
        if (preStart > preEnd || inStart > inEnd) {
            return null;
        }
        TreeNode node = new TreeNode(pre[preStart]);
        int inMid = inStart;
        while (in[inMid] != pre[preStart]) {
            inMid++;
        }
        int delta = inMid - inStart;
        node.left = construct(pre, in, preStart + 1, preStart + delta, inStart, inMid - 1);
        node.right = construct(pre, in, preStart + delta + 1, preEnd, inMid + 1, inEnd);
        return node;
    }
}
```

## 6. 从尾到头打印链表

递归

```java
public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        ArrayList<Integer> result = new ArrayList<>();
        if (listNode == null) {
            return result;
        }
        addList(listNode, result);
        return result;
    }
    
    private void addList(ListNode listNode, ArrayList<Integer> list) {
        if (listNode != null) {
            addList(listNode.next, list);
            list.add(listNode.val);
        } 
    }
}
```



## 5. 替换空格

```java
public class Solution {
    public String replaceSpace(StringBuffer str) {
        if (str == null || str.length() == 0) {
            return str.toString();
        }
        int blanks = 0;
        for (int i = 0; i < str.length(); i++) {
            if (str.charAt(i) == ' ') {
                blanks++;
            }
        }
        if (blanks == 0) {
            return str.toString();
        }
        int size = str.length() + 2 * blanks;
        char[] chars = new char[size];
        for (int i = str.length() - 1, j = size - 1; i >= 0; i--) {
            if (str.charAt(i) == ' ') {
                chars[j--] = '0';
                chars[j--] = '2';
                chars[j--] = '%';
            } else {
                chars[j--] = str.charAt(i);
            }
        }
        return new String(chars);
    }
}
```

## 4. 二维数组中的查找

> 给定一个二维数组，其每一行从左到右递增排序，从上到下也是递增排序。给定一个数，判断这个数是否在该二维数组中。

```java
public class Solution {
    public boolean Find(int target, int [][] array) {
        if (array == null || array.length == 0 || array[0] == null || array[0].length == 0) {
            return false;
        }
        int row = 0, column = array[0].length - 1;
        while (row < array.length && column >= 0) {
            if (array[row][column] == target) {
                return true;
            } else if (array[row][column] > target) {
                column--;
            } else {
                row++;
            }
        }
        return false;
    }
}
```

## 3. 数组中重复的数字

> 在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

要求时间复杂度 O(N)，空间复杂度 O(1)。因此不能使用排序的方法，也不能使用额外的标记数组。

对于这种数组元素在 [0, n-1] 范围内的问题，可以将值为 i 的元素调整到第 i 个位置上进行求解。本题要求找出重复的数字，因此在调整过程中，如果第 i 位置上已经有一个值为 i 的元素，就可以知道 i 值重复。

以 (2, 3, 1, 0, 2, 5) 为例，遍历到位置 4 时，该位置上的数为 2，但是第 2 个位置上已经有一个 2 的值了，因此可以知道 2 重复：

```java
public boolean duplicate(int[] nums, int length, int[] duplication) {
    if (nums == null || length <= 0)
        return false;
    for (int i = 0; i < length; i++) {
        while (nums[i] != i) {
            if (nums[i] == nums[nums[i]]) {
                duplication[0] = nums[i];
                return true;
            }
            swap(nums, i, nums[i]);
        }
    }
    return false;
}

private void swap(int[] nums, int i, int j) {
    int t = nums[i];
    nums[i] = nums[j];
    nums[j] = t;
}
```

