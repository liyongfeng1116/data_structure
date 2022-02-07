# 贪心算法（greedy algorithm）
## 思路：自顶向下，从局部最优到全局最优。需要证明的是从局部最优能到全局最优。用来逼近NP问题。
## 练习  
- [分发饼干leetcode455](https://leetcode-cn.com/problems/assign-cookies/)  
 
    1. 大饼干分配给胃口大的小孩。
    2. 小饼干先分配给胃口小的孩子。

![分发饼干](./pics/greedy/assigncookies.png)  
```
class Solution {
    public int findContentChildren(int[] g, int[] s) {
       Arrays.sort(g);
       Arrays.sort(s);
       int count = 0;
       int start = s.length - 1;
       for (int index = g.length - 1; index >= 0; index--) {
           if (start >= 0 && g[index] <= s[start]) {
               start--;
               count++;
           }
       }
       return count;
    }
}
```
- [摆动序列leetcode376](https://leetcode-cn.com/problems/wiggle-subsequence/)
    1. 只找到波峰和波谷的值
    2. 前后的差值，一个为正一个为负  
    3. 左边端点处，需要

![摆动序列](./pics/greedy/摆动序列.png)  
```
class Solution {
    public int wiggleMaxLength(int[] nums) {
        if (nums.length <= 1) {
            return nums.length;
        }
        int prediff = 0;    // 前一对差值
        int currdiff = 0;   // 当前差值
        int res = 1;        // 默认序列右边有一个峰值
        for (int i = 0; i < nums.length - 1; i++) {
            currdiff = nums[i+1] - nums[i];
            if ((currdiff > 0 && prediff <= 0) || (currdiff < 0 && prediff >= 0)) {
                res++;
                prediff = currdiff;
            }
        }
        return res;
    }
}
```
- [最大子数组和leetcode53](https://leetcode-cn.com/problems/maximum-subarray/)
    1. 当-2，1为头时肯定时以1开始子数组
    2. 局部最优，当前子数组和为负数，则从下一个开始
```
class Solution {
    /*
    // 暴力方法
    public int maxSubArray(int[] nums) {
        int count = 0;
        int res = Integer.MIN_VALUE;
        for (int i = 0; i < nums.length; i++) {
            count = 0;
            for (int j = i; j < nums.length; j++) {
                count += nums[j];
                if (count > res) {
                    res = count;
                }
            }
        }
        return res;
    }
    */
    // 贪心解法
    public int maxSubArray (int[] nums) {
        if (nums.length == 1) {
            return nums[0];
        }
        int res = Integer.MIN_VALUE;
        int count = 0;
        for (int i = 0; i < nums.length; i++) {
            count += nums[i];
            res = Math.max(res,count);
            if (count <= 0) {
                count = 0;
                // continue; 不能时continue，因为是和，所以把count置为0即可
            }
        }
        return res;
    }
}
```
- [买股票的最佳时机leetcode122](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)
    1. 低的时候买，高的时候卖
    2. 只有一直股票，只有卖了才能买
    3. 加入第一天买，第三天卖，则收益 = price[3] - price[1] = (price[3] - price[2]) + (price[2] - price[1])
    4. 因此我们只需要关心每天的正利润即可  
   
![股票价格leetcode122](./pics/greedy/股票价格.png)  
```
class Solution {
    public int maxProfit(int[] prices) {
        if (prices.length == 1) {
            return 0;
        }
        int sum = 0;
        for (int i = 0; i < prices.length - 1; i++) {
            int count = prices[i+1] - prices[i];
            if (count >= 0) {
                sum += count;
            }
        }
        return sum;
    }
}
```
- [跳跃游戏leetcode55](https://leetcode-cn.com/problems/jump-game/)   
    1. 不用关心每一次跳几步，应该关注覆盖的范围
    2. 考察覆盖范围是否能到最后一步
    3. 局部：每次取最大覆盖范围；整体：得到最大覆盖范围，是否能到最后
   
   ![跳跃游戏](./pics/greedy/跳跃游戏.png)
```
class Solution {
    public boolean canJump(int[] nums) {
        if (nums.length == 1) {
            return true;
        }
        int cover = 0;
        for (int i = 0; i <= cover; i++) {  // 在cover范围之中进行遍历
            int curr = i + nums[i];
            cover = Math.max(curr,cover);
            if (cover >= nums.length - 1) { // 只要length-1就行了
                return true;
            }
        }  
        return false;
    }
}
```