







# \判断环形链表

## 方法1 哈希表

遍历所有的节点并在哈希表中存储每个节点的引用，如果当前节点为null,说明无环，如果当前节点的引用已经在哈希表中，有环。

```
public boolean hasCycle(ListNode head) {
    Set<ListNode> nodesSeen = new HashSet<>();
    while (head != null) {
        if (nodesSeen.contains(head)) {
            return true;
        } else {
            nodesSeen.add(head);
        }
        head = head.next;
    }
    return false;
}

```

## 方法2 快慢指针

https://leetcode-cn.com/problems/linked-list-cycle/solution/zhe-shi-yi-ge-you-qu-de-shi-pin-ti-jie-kuai-man-zh/

```
    public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) {
            return false;
        }
        ListNode slow = head;
        ListNode fast = head.next;
        while(slow != fast) {
            if (fast == null || fast.next == null) {
                return false;
            }
            slow = slow.next;
            fast = fast.next.next;
        }
          return true;
    }


```

# LRU缓存机制

获取数据 get(key) - 如果关键字 (key) 存在于缓存中，则获取关键字的值（总是正数），否则返回 -1。
写入数据 put(key, value) - 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字/值」。当缓存容量达到上限时，它应该**在写入新数据之前删除最久未使用的数据值**，从而为新的数据值留出空间。

```
class LRUCache {
    Queue<Integer> queue;
    Map<Integer, Integer> map;
    Integer capacity;
    public LRUCache(int capacity) {
          this.map = new HashMap<>();
        this.queue = new LinkedList<>();
        this.capacity = capacity;
    }
    
    public int get(Integer key) {
      if (queue.contains(key))
        {
              //先remove是为了让该元素排到队列的后面，因为使用过了
                queue.remove(key);
                queue.add(key);
                return map.get(key);
        }
        else
        {
            return -1;
        }
    }
    
    public void put(int key, int value) {
            if (queue.contains(key))
        {
            queue.remove(key);
            queue.add(key);
            map.put(key, value);
        }
        else if (capacity == 0)
        {
         //删除最久未使用的元素
            map.remove(queue.poll());
            queue.add(key);
            map.put(key, value);
        }
        else
        {
            queue.add(key);
            map.put(key, value);
            capacity--;
        }
    }
}
/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

# 寻找重复数

https://leetcode-cn.com/problems/find-the-duplicate-number/solution/287-xun-zhao-zhong-fu-shu-by-ming-zhi-shan-you-m9r/

## 方法一 快慢指针思想

```
class Solution {
    public int findDuplicate(int[] nums) {
        int fast=0;
        int slow=0;
        while(true){
            slow=nums[slow];
            fast=nums[nums[fast]];
            if(fast==slow){
                slow=0;
                break;
            }
        }
        while(true){
            fast=nums[fast];
            slow=nums[slow];
            if(fast==slow){
                return fast;
            }
        }
   
    }
}
```

## 方法二 二分法思想

https://leetcode-cn.com/problems/find-the-duplicate-number/solution/er-fen-fa-si-lu-ji-dai-ma-python-by-liweiwei1419/

```
package Test1;
public class Solution {

    public int findDuplicate(int[] nums) {
        int len = nums.length;
        int left = 1;
        int right = len - 1;
        while (left < right) {
            // 在 Java 里可以这么用，当 left + right 溢出的时候，无符号右移保证结果依然正确
            int mid = (left + right) >>> 1
            int cnt = 0;
            for (int num : nums) {
                if (num <= mid) {
                    cnt += 1;
                }
            }
            // 根据抽屉原理，小于等于 4 的个数如果严格大于 4 个
            // 此时重复元素一定出现在 [1, 4] 区间里
            if (cnt > mid) {
                // 重复元素位于区间 [left, mid]
                right = mid;
            } else {
                // if 分析正确了以后，else 搜索的区间就是 if 的反面
                // [mid + 1, right]
                left = mid + 1;
            }
        }
        return left;
    }
}
```

# 可被k整除的子数组

https://www.jianshu.com/p/3ee55e584486

```
package Test1;
public class findArrK {
    /**
     * 整体思路：
     * 将所有数变成K对应的余数
     *      处理方式：正数就为余数  负数就为 (余数 + K) % K
     *      通用方式: (num % K + K) % K 【不用区分正负数】
     *
     * 通过求余数前缀和的进行中若有相同的余数那么做差就存在被整除的结果：
     * 例如：index= 0 1 2 3 4  5  6 7
     *          A={1,2,3,4,5,-1,-2,3} K=3
     *   =>preMod={1,2,3,1,2, 2, 1,3}
     *   =>sumMod={1,0,0,1,0, 2, 0,0}
     *   => sum[0:1] % 3 = 0
     *      sum[0:2] % 3 = 0
     *      sum[1:2] % 3 = 0
     *
     *   => 当前内容中余数出现的个数就是满足的个数
     **/
    public int subarraysDivByK(int[] A, int K) {
        // 存储前缀和出现余数的个数
        int[] modK = new int[K];
        modK[0] = 1; // 因为当余数是0的时候，直接满足不用进行做差处理

        int count = 0;
        int sum = 0; // 进行前缀和计算
        for(int tmp: A){
            sum = ((sum + tmp) % K + K) % K; // 余数处理
            count += modK[sum]; // 查看有多少个余数相同的个数
            modK[sum] ++; // 余数个数添加
        }
        return count;
    }
}
```

```
/**
 * 暴力解法，枚举所有连续数组的结果
 * @param A
 * @param K
 */
public static int forSolution(int[] A, int K) {
    int res = 0;
    for (int i = 0; i < A.length; i++) {
        int sum = A[i];
        if (sum % K == 0) ++res;
        for (int j = i + 1; j < A.length; j++) {
            sum += A[j];
            // 被Ｋ整除
            if (sum % K == 0) ++res;
        }
    }
    return res;
}
```

# 两数之和

## 暴力法

```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[j] == target - nums[i]) {
                    return new int[] { i, j };
                }
            }
        }
        throw new IllegalArgumentException("No two sum solution");
    }
}
```

## 哈希表

https://leetcode-cn.com/problems/two-sum/solution/liang-shu-zhi-he-by-leetcode-2/

```
package Test1;
import java.util.HashMap;
import java.util.Map;
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            map.put(nums[i], i);
        }
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement) && map.get(complement) != i) {
                return new int[] { i, map.get(complement) };
            }
        }
        throw new IllegalArgumentException("No two sum solution");
    }
}

```

# 三数之和

https://leetcode-cn.com/problems/3sum/solution/javashi-pin-jiang-jie-xi-lie-3-sum-by-sean-kuang/

```
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
         List<List<Integer>> res=new ArrayList<>();
         if (nums.length<3){
             return res;
         }
        Arrays.sort(nums); //对数组进行排序 -5 1 2 3 4
          for(int i=0;i<nums.length;i++){
           if (i>0&&nums[i]==nums[i-1]){
                  continue;
              }
               int left=i+1; //左指针
               int right=nums.length-1; //右指针
              int target=-nums[i];
              while (left<right){
                  if(target==nums[left]+nums[right]){
                      ArrayList<Integer> list=new ArrayList<>();
                      list.add(-target);
                      list.add(nums[left]);
                      list.add(nums[right]);
                        res.add(list);
                      left++;
                      right--;
                        while(left<nums.length&&nums[left]==nums[left-1]){
                      left++;
                  }
                  while(right>left&&nums[right]==nums[right+1]){
                      right--;
                  }
                  }else if(target>nums[left]+nums[right]){
                      left++;
                  }else{
                      right--;
                  }
              }
          }

           return res;

    }
}
```



# 最接近的三数之和

## 方法1：暴力解法:三种循环

```
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        int min=nums[0]+nums[1]+nums[2];
        
           for(int i=0;i<nums.length;i++){
                for(int j=i+1;j<nums.length;j++){
                     for( int k=j+1;k<nums.length;k++){
                           int sum=nums[i]+nums[j]+nums[k];
                           if(Math.abs(target-min)>=Math.abs(target-sum)){
                                 min=sum;
                           }
                     }
                }
           }
           return min;
    }
}
```

## 方法2：排序+双指针

https://leetcode-cn.com/problems/3sum-closest/solution/zui-jie-jin-de-san-shu-zhi-he-by-leetcode-solution/

```
class Solution {
    public int threeSumClosest(int[] nums, int target) {
          Arrays.sort(nums);
        int res=nums[0]+nums[1]+nums[2];
        for(int i=0;i<nums.length;i++){
            if(i>0&&nums[i]==nums[i-1]){
                continue;
            }
            int ans=nums[i];
            int left=i+1;
            int right=nums.length-1;
            while(left<right){
                int sum=nums[i]+nums[left]+nums[right];
                if(sum==target){
                    return target;
                }
                if(Math.abs(sum-target)<=Math.abs(res-target)){
                    res=sum;
                }
                if(sum>target){
                    right--;
                    while (right>left&&nums[right]==nums[right+1]){
                     right--;
                    }
                }else{
                    left++;
                    while(left<nums.length&&nums[left]==nums[left-1]){
                     left++;
                    }
                }

                }

            }
            return res;
    }
}
```



# 字符串解码

## 方法1 栈操作

https://leetcode-cn.com/problems/decode-string/solution/zi-fu-chuan-jie-ma-by-leetcode-solution/

```
class Solution {
    int ptr=0;
    public String decodeString(String s) {
        LinkedList<String> ls=new LinkedList<>();
          while(ptr<s.length()){
                char c=s.charAt(ptr);
                 if(Character.isDigit(c)){
                     //如果是数字
                   ls.add(getDigits(s));
                 
               }else if(Character.isLetter(c)||'['==c){
                //    是字母或者是[
                      ls.add(String.valueOf(s.charAt(ptr++)));
               }else {
                       ++ptr;
                    LinkedList<String> sub=new LinkedList<>();
                    while(!"[".equals(ls.peekLast())){
                        sub.add(ls.removeLast());
                    }   
                    Collections.reverse(sub);
                    ls.removeLast();  //左括号出栈
                    //此时栈顶为数字
                    int time=Integer.parseInt(ls.removeLast());
                    String res=getstr(sub);
                    StringBuilder sb=new StringBuilder();
                    while(time!=0){
                       sb.append(res);
                       time--;
                    }
                    ls.add(sb.toString());
               }
          }
          return getstr(ls);
        
    }
    public String getstr(LinkedList<String> sub){
         StringBuilder sb=new StringBuilder();
          for(String s:sub){
            sb.append(s);
          }
          return sb.toString();
    }
    public String getDigits(String s){
     //此方法为了防止出现大于等于两位的数字 "10[abc]"
          StringBuilder sb=new StringBuilder();
          while(Character.isDigit(s.charAt(ptr))){
               sb.append(s.charAt(ptr++));
          }
          return sb.toString();
    }

}
```

# 打家劫舍

## 方法1 动态规划+滚动数组

https://leetcode-cn.com/problems/house-robber/solution/da-jia-jie-she-by-leetcode-solution/



```
package Test1;
public class Solution {
        public int rob(int[] nums) {
            if (nums == null || nums.length == 0) {
                return 0;
            }
            int length = nums.length;
            if (length == 1) {
                return nums[0];
            }
            int[] dp = new int[length];
            dp[0] = nums[0];
            dp[1] = Math.max(nums[0], nums[1]);
            for (int i = 2; i < length; i++) {
                dp[i] = Math.max(dp[i - 2] + nums[i], dp[i - 1]);
            }
            return dp[length - 1];
        }
}

```

# 打家劫舍二(收尾连接)

https://leetcode-cn.com/problems/house-robber-ii/solution/213-da-jia-jie-she-iidong-tai-gui-hua-jie-gou-hua-/

```
class Solution {  
    public int rob(int[] nums) {       
        if (nums == null || nums.length == 0) {
                return 0;
            }
            int length = nums.length;
            if (length == 1) {
                return nums[0];
            }
    return Math.max(robbb(Arrays.copyOfRange(nums,0,nums.length-1)),robbb(Arrays.copyOfRange(nums,1,nums.length)));
}
  public int robbb(int[] nums){
       if (nums == null || nums.length == 0) {
                return 0;
            }
            int length = nums.length;
            if (length == 1) {
                return nums[0];
            }
            int[] dp = new int[length];
            dp[0] = nums[0];
            dp[1] = Math.max(nums[0], nums[1]);
            for (int i = 2; i < length; i++) {
                dp[i] = Math.max(dp[i - 2] + nums[i], dp[i - 1]);
            }
            return dp[length - 1];
  }

}
```

# 打家劫舍三

## 方法1：递归

https://leetcode-cn.com/problems/house-robber-iii/solution/san-chong-fang-fa-jie-jue-shu-xing-dong-tai-gui-hu/

```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int rob(TreeNode root) {
      if(root==null){
          return 0;
      }
      int money=root.val;
      if(root.left!=null){
        money+=rob(root.left.left)+rob(root.left.right);
      }
      if(root.right!=null){
        money+=rob(root.right.left)+rob(root.right.right);
      }
      return Math.max(money,(rob(root.left)+rob(root.right)));
    }
}
```

## 方法2：利用hashmap进行优化

https://leetcode-cn.com/problems/house-robber-iii/solution/san-chong-fang-fa-jie-jue-shu-xing-dong-tai-gui-hu/

```
public int rob(TreeNode root) {
    HashMap<TreeNode, Integer> memo = new HashMap<>();
    return robInternal(root, memo);
}
public int robInternal(TreeNode root, HashMap<TreeNode, Integer> memo) {
    if (root == null) return 0;
    if (memo.containsKey(root)) return memo.get(root);
    int money = root.val;
    if (root.left != null) {
        money += (robInternal(root.left.left, memo) + robInternal(root.left.right,       memo));
    }
    if (root.right != null) {
        money += (robInternal(root.right.left, memo) + robInternal(root.right.right, memo));
    }	
    int result = Math.max(money, robInternal(root.left, memo) + robInternal(root.right, memo));
    memo.put(root, result);
    return result;
}

```

# 柱状图中的最大矩形

https://blog.csdn.net/Zolewit/article/details/88863970

## 暴力解法

```
class Solution {
    public int largestRectangleArea(int[] heights) {
          int area=0,n=heights.length;
          for(int i=0;i<n;i++){
            int w=1,h=heights[i],j=i;
            while(--j>=0&&heights[j]>=h){
                w++;
            }
            i=j;
            while(++j<n&&heights[j]>=h){
                w++;
            }
            area=Math.max(area,w*h);
          } 
          return area;
    }
}
```

## 单调栈

https://leetcode-cn.com/problems/largest-rectangle-in-histogram/comments/

```
package Test1;

import java.util.ArrayDeque;
import java.util.Deque;

public class Solution3 {
    public int largestRectangleArea(int[] heights) {
        // 这里为了代码简便，在柱体数组的头和尾加了两个高度为 0 的柱体。
        int[] tmp = new int[heights.length + 2];
        System.arraycopy(heights, 0, tmp, 1, heights.length);

        Deque<Integer> stack = new ArrayDeque<>();
        int area = 0;
        for (int i = 0; i < tmp.length; i++) {
            // 对栈中柱体来说，栈中的下一个柱体就是其「左边第一个小于自身的柱体」；
            // 若当前柱体 i 的高度小于栈顶柱体的高度，说明 i 是栈顶柱体的「右边第一个小于栈顶柱体的柱体」。
            // 因此以栈顶柱体为高的矩形的左右宽度边界就确定了，可以计算面积🌶️ ～
            while (!stack.isEmpty() && tmp[i] < tmp[stack.peek()]) {
                int h = tmp[stack.pop()];
                area = Math.max(area, (i - stack.peek() - 1) * h);
            }
            stack.push(i);
        }

        return area;
    }
}

```

# 对称二叉树

https://leetcode-cn.com/problems/symmetric-tree/solution/dui-cheng-er-cha-shu-by-leetcode-solution/

```
 public boolean isSymmetric(TreeNode root) {
        return check(root, root);
    }

    public boolean check(TreeNode p, TreeNode q) {
        if (p == null && q == null) {
            return true;
        }
        if (p == null || q == null) {
            return false;
        }
        return p.val == q.val && check(p.left, q.right) && check(p.right, q.left);
    }
```

# 拥有最多糖果的人

https://leetcode-cn.com/problems/kids-with-the-greatest-number-of-candies/solution/yong-you-zui-duo-tang-guo-de-hai-zi-by-leetcode-so/

```
class Solution {
    public List<Boolean> kidsWithCandies(int[] candies, int extraCandies) {
          int max=0;
          for(int i=0;i<candies.length;i++){
            max=Math.max(max,candies[i]);  //找出数组中的最大值
          }
          List<Boolean> list=new ArrayList<>();
          for(int j=0;j<candies.length;j++){
             list.add(candies[j]+extraCandies>=max);
          }
          return list;
    }
}
```

# 无重复字符的最长子串

https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/solution/wu-zhong-fu-zi-fu-de-zui-chang-zi-chuan-cshi-xian-/

```
class Solution {
    public int lengthOfLongestSubstring(String s) {
       if (s == null) {
            return 0;
        }
        //2.
        int len = s.length();
        List<Character> list = new ArrayList<>();
        int count = 0;
        for (int i = 0; i < len; i++) {
            for (int j = i; j < len; j++) {
                if (list.contains(s.charAt(j))) {
                    break;
                } else {
                    list.add(s.charAt(j));
                }
            }
            count = Math.max(count, list.size());
            //当count大于等于剩下字符串的长度时，不再遍历
            if (count >= len - 1 - i) {
                break;
            }
            list.clear(); //清空list里所有的引用
        }
        return count;
    }
}
```

# 求1+2...+n的值(特殊题********)

https://leetcode-cn.com/problems/qiu-12n-lcof/

## 方法1(工具类)

```
int sum = IntStream.rangeClosed(1, 5).sum(); 求出[1,5]的和值
```

## 方法2(等差数列求和)

```cpp
  public int sumNums(int n) {
        return (int) (Math.pow(n, 2) + n) >> 1;
    }
```

## 方法3(特殊方法)

```java
class Solution {
    int[] test=new int[]{0};
    public int sumNums(int n) {
        try{
            return test[n];
        }catch(Exception e){
            return n+sumNums(n-1);
        }
    }
}
```

# 除自身以外数组的乘积

空间复杂度为o(n)

```
package Test1;

public class Solution2 {
    public int[] productExceptSelf(int[] nums) {
          int len=nums.length;
          int[] R =new int[len];
          int[] L=new int[len];
          int[] res=new int[len];
//          算出左边的乘积
        L[0]=1;
        for (int i = 1; i < L.length; i++) {
            L[i]=L[i-1]*nums[i-1];
        }
//        算出右边的乘积
        R[len-1]=1;
        for(int j=len-2;j>=0;j--){
            R[j]=R[j+1]*nums[j+1];
        }
        for(int i=0;i<len;i++){
           res[i]=L[i]*R[i];
        }
        return res;
    }
}
```



空间复杂度为o(1) **输出数组不算在空间复杂度中**

```
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int len=nums.length;
        //算出左边数的乘积
        int res[] = new int[len];   
        // 左边第一个数的左边没有 默认就是1
        res[0]=1;
        for(int i=1;i<len;i++){
            res[i]=res[i-1]*nums[i-1];
        }
        // 最右边也是没数 默认是1
      int R=1;
        for(int j=len-1;j>=0;j--){
            res[j]=R*res[j];
            R=R*nums[j];
        }
        return res;
    }
}
```

# 顺时针打印矩阵

```
class Solution {
    public int[] spiralOrder(int[][] matrix) {
//         l        r
         //1  2  3  4  u
         //5  6  7  8
         //9  10 11 12
         //13 14 15 16  d
        //l--> r  dir ↓ u++
        //u-->d   dir ← r--
        //r-->l   dir ↑ d--
        //d-->u   dir → l++
        int row=matrix.length;
            if(row==0){
                return new int[0];
            }
        int col=matrix[0].length;
        int[] ret=new int[row*col];
        int left=0;
        int right=col-1;
        int upper=0;
        int down=row-1;
        int index=0;
        while(index<ret.length){
            for(int i=left;i<=right;i++){
                ret[index++]=matrix[upper][i];
                if (index==ret.length){
                    return ret;
                }
            }
            upper++;
            for(int i=upper;i<=down;i++){
                ret[index++]=matrix[i][right];
                if (index==ret.length){
                    return ret;
                }
            }
            right--;
            for(int i=right;i>=left;i--){
               ret[index++]=matrix[down][i];
                if (index==ret.length){
                    return ret;
                }
            }
            down--;
            for(int i=down;i>=upper;i--){
                ret[index++]=matrix[i][left];
                if (index==ret.length){
                    return ret;
                }
            }
            left++;

        }
        return ret;
    }
}
```

# 回文数(最好不用字符串)

https://leetcode-cn.com/problems/palindrome-number/solution/hui-wen-shu-by-leetcode-solution/#comment

## 方法1 (转为字符串)

```
public boolean isPalindrome(int x) {
        String res=String.valueOf(x);
        int len=res.length();
        if(len==1){
            return true;
        }
        for(int i=0;i<len;i++){
            if (i==len-i-1){
                return true;
            }
                if(res.charAt(i)!=res.charAt(len-i-1)){
                    return false;
                }

        }
        return true;

    }
```

## 方法2(StringBuilder)

```
  public boolean isPalindrome(int x, boolean b) {
        if (x < 0) {
            return false;
        }
        StringBuilder sb = new StringBuilder(String.valueOf(x));
        return sb.toString().equals(sb.reverse().toString());
    }
```

## 方法3(反转一半数字)

![image-20200610200814813](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200610200814813.png)

```
class Solution {
    public boolean isPalindrome(int x) {
        // 反转一半数字
        if(x<0||(x%10==0&&x!=0)){
             return false;
        }
        int reverseNum=0;
        while(x>reverseNum){
            reverseNum=reverseNum*10+x%10;
            x/=10;
        }
        //分奇数偶数情况
        return x==reverseNum||x==reverseNum/10;

    }
}
```

# 转变数组后最接近目标值的数组和

https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/solution/bian-shu-zu-hou-zui-jie-jin-mu-biao-zhi-de-shu-zu-/

## 方法1 ：枚举+二分查找

```
class Solution {
            public int findBestValue(int[] arr, int target) {
                        Arrays.sort(arr);
                int n = arr.length;
                int[] prefix = new int[n + 1];
                for (int i = 1; i <= n; ++i) {
                    prefix[i] = prefix[i - 1] + arr[i - 1];
                }
                int r = arr[n - 1];
                int ans = 0, diff = target;
                for (int i = 1; i <= r; ++i) {
                    int index = Arrays.binarySearch(arr, i);
                    if (index < 0) {
                        index = -index - 1;
                    }
                    int cur = prefix[index] + (n - index) * i;
                    if (Math.abs(cur - target) < diff) {
                        ans = i;
                        diff = Math.abs(cur - target);
                    }
                }
                return ans;
            }
}
```

# 最长公共前缀

https://leetcode-cn.com/problems/longest-common-prefix/

```
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if(strs.length==0||strs==null){
            return "";
        }
        String res=strs[0];
        int len=strs.length;
        for(int i=1;i<len;i++){
          res=compare(res,strs[i]);
          if(res.length()==0){
                break;
          }            
        }
        return res;
    }

    public String compare(String str1,String str2){
        int length=Math.min(str1.length(),str2.length());
        int index=0;
        while(index<length&&str1.charAt(index)==str2.charAt(index)){
           index++;
        }
        return str1.substring(0,index);
    }
}
```

# 最佳观光组合

https://leetcode-cn.com/problems/best-sightseeing-pair/

```
class Solution {
public:
    int maxScoreSightseeingPair(vector<int>& A) {
        int ans=0;
        int mx=A[0]+0;
        //利用A[j]-j不变，寻找A[i]+i的最大值
        for(int j=1;j<A.size();j++){
           ans=max(ans,mx+A[j]-j);
           mx=max(mx,A[j]+j);
        }
        return ans;
    }
};
```

# 先序遍历还原二叉树(利用栈)

https://leetcode-cn.com/problems/recover-a-tree-from-preorder-traversal/submissions/

```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* recoverFromPreorder(string S) {
       stack<TreeNode *> path;
       int pos=0;
       while(pos<S.size()){
             int level=0;
             while(S[pos]=='-'){
                ++level;
                ++pos;
             }
             int value=0;
             while(pos<S.size()&&isdigit(S[pos])){
                 //算出数字的值
               value=value*10+(S[pos]-'0');
               ++pos;
             }
             TreeNode* node=new TreeNode(value);
             if(level==path.size()){
                if(!path.empty()){
                 path.top()->left=node;
                }
             }
             else{
                 while(level!=path.size()){
                   path.pop();
                 }
                 path.top()->right=node;
             }
             path.push(node);
       }
       while(path.size()>1){
              path.pop();
       }
       return path.top();

    }
};
```

# 验证回文数

https://leetcode-cn.com/problems/valid-palindrome/

```
package Test1;

public class SixNighteen {
    public boolean isPalindrome(String s) {
        if(s.length()==0){
              return true;
        }
        StringBuilder sb=new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
           if((s.charAt(i)>=48&&s.charAt(i)<=57)||(s.charAt(i)>=65&&s.charAt(i)<=90)||((s.charAt(i)>=97&&s.charAt(i)<=122))){
               sb.append(s.charAt(i));
           }
        }
        if (sb.toString().equalsIgnoreCase(sb.reverse().toString())){
            return true;
        }
        return false;
    }
}

```

# 回文子串

https://leetcode-cn.com/problems/palindromic-substrings/

## 方法1(动态规划)

```
class Solution {
    public int countSubstrings(String s) {
        if(s==null||s.length()==0){
          return 0;
        }
        int len=s.length();
        boolean[][] res=new boolean[len][len];
        for(int i=0;i<len;i++){
             res[i][i]=true;
        }
        int resNum=len; //单个字符都满足
        for(int i=len-1;i>=0;i--){
               for(int j=i+1;j<len;j++){
                   if(s.charAt(i)==s.charAt(j)){
                        if(j-i==1){
                             res[i][j]=true;
                        } else{
                            res[i][j]=res[i+1][j-1];
                        }
                   }else{
                        res[i][j]=false;
                   }
                   if(res[i][j]){
                         resNum++;
                   }
               }
        }
        return resNum;
    }
}
```

# 增减字符串匹配

https://leetcode-cn.com/problems/di-string-match/

```
class Solution {
    public int[] diStringMatch(String S) {
          int len=S.length();
          int N=len;
          int[] res=new int[len+1];
          int low=0,high=N;
          for(int i=0;i<N;i++){
            if(S.charAt(i)=='I'){
               res[i]=low++;
            }else{
                res[i]=high--;
            }
          }
          //最终都会相遇
          res[N]=low;
          return res;
    }
}
```

# 二进制求和

https://leetcode-cn.com/problems/add-binary/solution/er-jin-zhi-qiu-he-by-leetcode-solution/

```
class Solution {
public:
    string addBinary(string a, string b) {
        string ans;
		reverse(a.begin(),a.end());
		reverse(b.begin(),b.end());
		int n = max(a.size(), b.size()), carry = 0;
		for (int i = 0; i < n;i++) {
			carry += i < a.size() ? (a.at(i) == '1') : 0;
			carry += i < b.size() ? (b.at(i) == '1') : 0;
			ans.push_back((carry % 2)?'1':'0');
			carry /= 2;
		}
		if (carry) {
			ans.push_back('1');
		}
		reverse(ans.begin(),ans.end());
		return ans;
    }
};
```

# 最大公共子序列

**动态规划**

```
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {

        int table[][] = new int[text1.length() + 1][text2.length() + 1];

        for (int col=0; col<table[0].length; col++) {
            table[0][col] = 0;
        }

        for (int row=0; row<table.length; row++) {
            table[row][0] = 0;
        }

        for (int row=1; row<table.length;row++) {
            char ch1 = text1.charAt(row-1);
            for(int col=1; col<table[row].length; col++) {
                char ch2 = text2.charAt(col-1);
                if(ch1 == ch2) {
                    table[row][col] = table[row-1][col-1] + 1;
                } else {
                    table[row][col] = Math.max(table[row-1][col], table[row][col-1]);
                }
            }
        }

        int[] lastRow = table[table.length-1];
        return lastRow[lastRow.length-1];
    }
}
```

# 钥匙与房间

https://leetcode-cn.com/problems/keys-and-rooms/

dfs和bfs都可行

```
package com.cbx;

import java.util.ArrayList;
import java.util.List;

/**
 * @Author:cbx
 * @Date:2020/08/31/14:12
 */

/**
 * 可以看成一个有向图
 * dfs和bfs都是可以的
 */
public class 钥匙和房间 {
    static boolean[] visited;
    static int num;
    public static void main(String[] args) {
        List<Integer> list1=new ArrayList<>();
        list1.add(1);
        List<Integer> list2=new ArrayList<>();
        list1.add(2);
        List<Integer> list3=new ArrayList<>();
        list3.add(3);
        List<Integer> list4=new ArrayList<>();
        List<List<Integer>> rooms=new ArrayList<>();
        rooms.add(list1);
        rooms.add(list2);
        rooms.add(list3);
        rooms.add(list4);
        boolean b = canVisitAllRooms(rooms);
        System.out.println(b);
    }

    public  static boolean canVisitAllRooms(List<List<Integer>> rooms) {
        int size = rooms.size();
        num=0;
        visited=new boolean[size];
        dfs(rooms,0);
        return num==size;
    }

    private static void dfs(List<List<Integer>> rooms, int i) {
        visited[i]=true;
        num++;
        for (int it : rooms.get(i)) {
             if (!visited[it]){
                 dfs(rooms,it);
             }
        }
    }

}

```

# 预测赢家(动态规划)

```
package com.cbx;

import java.util.Map;

/**
 * @Author:cbx
 * @Date:2020/09/01/10:21
 * https://leetcode-cn.com/problems/predict-the-winner/
 */
 //动态规划思想，自底向上，找出状态方程
public class 预测赢家 {
    public static void main(String[] args) {
        int[] nums={1, 5, 2};
        boolean winner = PredictTheWinner(nums);
        System.out.println(winner);
    }

    public static boolean PredictTheWinner(int[] nums) {
      int length=nums.length;
      int[][] dp=new int[length][length];
        for (int i = 0; i < length; i++) {
            dp[i][i]=nums[i];
        }
        for (int i = length-1; i >=0; i--) {
            for (int j = i+1; j < length; j++) {
                dp[i][j]= Math.max(nums[i]-dp[i+1][j],nums[j]-dp[i][j-1]);
            }
        }
        return dp[0][length-1]>=0;

    }



}

```

# 石子游戏(动态规划)

```
public static boolean stoneGame(int[] piles) {
        int length = piles.length;
        int[][] dp=new int[length][length];
        for (int i = 0; i < length; i++) {
            dp[i][i]=piles[i];
        }
        //动态规划 自底向上
        for(int i=length-2;i>=0;i--){
            for(int j=i+1;j<length;j++){
                dp[i][j]=Math.max(piles[i]-dp[i+1][j],piles[j]-dp[i][j-1]);
            }
        }
        return dp[0][length-1]>0;
    }
```

