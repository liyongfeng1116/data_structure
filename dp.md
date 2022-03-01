# 动态规划
## 动态规划五部曲
1. 确定dp数组及以及下标的含义
2. 确定递推关系
3. dp数组初始化
4. 确定遍历顺序
5. 举例推导dp数组  
## 注意事项
[斐波那契数列leetcode509](https://leetcode-cn.com/problems/fibonacci-number/)
```
class Solution {
    public int fib(int n) {
        if (n < 2) {
            return n;
        }
        // dp数组及dp[i]的含义表示当前的数值
        int[] dp = new int[n+1];
        // 初始化
        dp[0] = 0;
        dp[1] = 1;
        // 遍历方向
        for (int i = 2; i <= n; i++) {
            // 递推关系
            dp[i] = dp[i-2] + dp[i-1];
        }
        return dp[n];
    }
}
```

## 题目
[爬楼梯leetcode70](https://leetcode-cn.com/problems/climbing-stairs/)
思路：  
1. 数组表示目前爬到i楼需要的方法为dp[i]
2. 递推关系：1楼1种方式，2楼两种方式，3楼在1楼和2楼基础上推导，dp[i] = dp[i-1] + dp[i-2] 
3. 初始化，dp[1] = 1; dp[2] = 2;
4. 从前向后
```
class Solution {
    public int climbStairs(int n) {
        if (n < 3) {
            return n;
        }
        int[] dp = new int[n+1];
        dp[1] = 1;
        dp[2] = 2;
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i-1] + dp[i-2];
        }
        return dp[n];
    }
}
```
[最小花费爬楼梯leetcode746](https://leetcode-cn.com/problems/min-cost-climbing-stairs/)
思路：
1. 数组表示爬到i层时的最小花费为dp[i]
2. 递推关系： 只关心最小花费，不关心走了多少步 dp[i] = min(dp[i-1],dp[i-2]) + cost[i]
3. 初始化，dp[0]和dp[1]
4. 从前向后
5. 最后一步不花钱，因为倒数第二步和倒数第一步的最小值已经交钱了
```
class Solution {
    public int minCostClimbingStairs(int[] cost) {
        
        int n = cost.length;
        if (n < 2) {
            return cost[n];
        }
        int[] dp = new int[n];
        dp[0] = cost[0];
        dp[1] = cost[1];
        for (int i = 2; i < n; i++) {
            dp[i] = Math.min(dp[i-1],dp[i-2]) + cost[i];
        }
        // 最后一步不用花钱
        return Math.min(dp[n-1],dp[n-2]);
    }
}
```
[不同路径leetcode62](https://leetcode-cn.com/problems/unique-paths/)
思路:
1. 类似于找二叉树的叶子结点，用dfs，**超时**
2. 数组d[i][j]表示走到这(i,j)有几种不同路径
3. 递推关系：d[i][j] = d[i-1][j] + d[i][j-1]
4. 初始化：上边界和左边界没有上届和左界，且只有一条路，置为1
5. 返回到m，n时刚好是结果
```
class Solution {
    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n];
        for (int i = 0; i < m; i++) {
            dp[i][0] = 1;
        } 
        for (int i = 0; i < n; i++) {
            dp[0][i] = 1;
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
}
```
[有遮挡不同路径leetcode63](https://leetcode-cn.com/problems/unique-paths-ii/)
思路：
1. d[i][j]表示走到(i,j)有几种路径
2. 递推关系：当该点为障碍，跳过；dp[i][j] = dp[i-1][j] + dp[i][j-1];
3. 初始化：如果边界出现，则边界该位置之后的都不可到达，全为1
```
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        int[][] dp = new int[m][n];
        for (int i = 0; i < m; i++) {
            if (obstacleGrid[i][0] == 1) {
                break;
            }
            dp[i][0] = 1;
        }
        for (int i = 0; i < n; i++) {
            if (obstacleGrid[0][i] == 1) {
                break;
            }
            dp[0][i] = 1;
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (obstacleGrid[i][j] == 1) {
                    continue;
                }
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    } 
}
```
[整数拆分leetcode343](https://leetcode-cn.com/problems/integer-break/)   
思路：
1. dp[i]表示拆分i所得到的最大乘积
2. 递推公式：将一个整数i分成两份的乘积，为j x (i-j), j 属于1~i-1  
            将一个整数拆分成多份的乘积，只需要将i-j继续拆分，即 j x dp[i-j];因此当j固定，dp[i] = max(jx(i-j),max(j x (i-j),j x dp[i-j])) 
3. 遍历顺序，j应该从1~i-1;
4. 初始化，dp[2] = 2;
```
class Solution {
    public int integerBreak(int n) {
        int [] dp = new int[n+1];
        dp[2] = 1;
        for (int i = 3; i <= n; i++) {
            int curmax = 0;
            for (int j = 1; j < i; j++) {
                curmax = Math.max(curmax, Math.max(j * (i-j), j * dp[i-j]));
            }
            dp[i] = curmax;
        }
        return dp[n];
    }
}
```
[不同的二叉搜索树leetcode96](https://leetcode-cn.com/problems/unique-binary-search-trees/)
思路：
![不同的二叉搜索树1](./pics/dp/不同二叉搜索树1.png)
![不同的二叉搜索树2](./pics/dp/不同二叉搜索树2.png)
元素1为头结点搜索树的数量 = 右子树有2个元素的搜索树数量 * 左子树有0个元素的搜索树数量

元素2为头结点搜索树的数量 = 右子树有1个元素的搜索树数量 * 左子树有1个元素的搜索树数量

元素3为头结点搜索树的数量 = 右子树有0个元素的搜索树数量 * 左子树有2个元素的搜索树数量  

dp[3] = dp[2]Xdp[0] + dp[1]Xdp[1] + dp[0]Xdp[2]
1. dp[i]表示i个结点包含的二叉搜索树的数量
2. 递推公式：dp[i] += dp[j-1]Xdp[i-j] 
3. 初始条件：dp[0] = 1;(防止变为0) dp[1] = 1;
4. 遍历方向，从前往后，j从1~i;
## 背包问题
![背包问题概图](./pics/dp/背包问题概图.png)  
基础背包问题：
有n件物品和一个最多能背重量为w 的背包。第i件物品的重量是weight[i]，得到的价值是value[i] 。每件物品只能用一次，求解将哪些物品装入背包里物品价值总和最大。  
### 二维dp数组01背包
1. 确定dp数组及下标的含义  
   
    **dp[i][j] 表示从下标为[0~i]的物品中任意取，放进背包容量为j的背包，价值总和最大**为多少  

    ![背包图像](./pics/dp/背包图像.png)
2. 确定递推公式  

   背包不放物品：d[i][j] = dp[i-1][j]; 即背包大小为j时，背包里无法放入物品，因此和上一次相同   

   放物品i:dp[i-1][j-weight[i]]为背包容量为j-weight[i]的时候不放物品i的最大价值；  

   当加入物品i时，dp[i-1][j-weight[i]]+value[i]\(物品i的价格) 即为背包放入i时的最大价值   

   状态转移方程 dp[i][j] = max(dp[i-1][j], dp[i-1][j-weight[i]] + value[i]);
3. 初始化  
   ***初始化一定要和dp数组的定义吻合，否则递推公式会乱***   

   ![背包初始化](./pics/dp/背包初始化.png)   

   如果背包容量为0，背包总价值肯定为0   

   根据状态转移方程，状态i由i-1推到出来，因此i=0的时候一定要初始化   

   dp[0][j],即i为0，存放编号0的物品的时候，各个背包容量的存放的最大价值  

   因此，当j<weight[0]的时候，dp[0][j] = 0, 因为背包放不下物品0；  
   当j >= weight[0]时，dp[0][j] = value[0];    

```
for (int j = 0; j < weight[0]; j++) {
    dp[0][j] = 0;
}
for (int j = weight[0]; j <= bagweight; j++>) {
    dp[0][j] = value[0];
}
```
   其他下标初始化，由递推公式可知，dp[i][j]是从左上方数值推导出来的，因此其他下标什么数值都可以。统一设置为0。  

   ![背包初始化2](./pics/dp/背包初始化2.jfif)
   
4. 遍历方向：
   两个方向，物品和背包重量   
   一般先遍历物品，再遍历背包重量  
```
for (int j = 0; j <= bagweight; j++) {
    for (int i = 1; i < weight.size(); i++) {
        if (j < weight[i]) {
            dp[i][j] = dp[i-1][j];
        } else {
            dp[i][j] = Math.max(dp[i-1][j], dp[i-1][j-weight[i]] + value[i]);
        }
    }
}
```
```
public static void main(string[] args){
    int[] weight = {1,3,4};
    int[] value = {15,20,30};
    int bagsize = 4;
    weightbagproblem(weight, value, bagsize);
}

public static void weightbagproblem(int[] weight, int[] value, int bagsize){
    int wlen = weight.length;
    int value0 = 0;

    // 定义dp数组，dp[i][j]表示背包容量为j时，前i个物品获得的最大价值
    int[][] dp = new int[wlen+1][bagsize+1];

    // 初始化，背包容量为0时，能获得的价值都为0
    for (int i = 0; i <= wlen; i++) {
        dp[i][0] = value0;
    }

    // 遍历顺序，先遍历物品，再遍历背包容量
    for (int i = 1; i < wlen; i++) {
        for (int j = 1; j <= bagsize; j++) {
            if (j < weight[i-1]) {
                dp[i][j] = dp[i-1][j]; 
            } else {
                dp[i][j] = Math.max(dp[i-1][j], dp[i-1][j-weight[i-1]] + value[i-1]);
            }
        }
    }

    for (int i = 0; i <= wlen; i++>) {
        for (int j = 0; j < bagsize; j++>) {
            System.out.print(dp[i][j] + " ");
        }
        System.out.print("\n");
    }
}
```
### 滚动数组01背包
1. dp数组的定义  
   **dp[j]表示容量为j的背包，所背的物品价值最大为dp[j]**
2. 一维数组的递推公式  
   dp[j] = max(dp[j],dp[j-weight[i]]+value[i])
3. 一维数组初始化  
    dp[0] = 0;容量为0，价值为0; dp数组在推导的时候取价值最大的数，初始将非0的设置为0
4. 一维数组遍历方式  
   **先物品后背包**  
   **背包从后往前遍历**

```
for (int i = 0; i < weight.size(); i++) {  // 先遍历物品
    for (int j = bagweight; j >= weight[i]; j--) {  //背包从后往前遍历
        dp[j] = Math.max(dp[j], dp[j - weight[i]] + value[i]);
    }
}
```
```
public static void bagProblem(int[] weight, int[] value, int bagweight) {
    int wlen = weight.length;
    int[] dp = new int[bagweight + 1];
    for (int i = 0; i < wlen; i++) {
        for (int j = bagweight; j >= weight[i]; j--) {
            dp[j] = Math.max(dp[j],dp[j-weight[i]] + value[i]);
        }
    }
}
```
---
[分割等和子集leetcode416](https://leetcode-cn.com/problems/partition-equal-subset-sum/)  

给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

注意: 每个数组中的元素不会超过 100 数组的大小不会超过 200

示例 1: 输入: [1, 5, 11, 5] 输出: true 解释: 数组可以分割成 [1, 5, 5] 和 [11].

示例 2: 输入: [1, 2, 3, 5] 输出: false 解释: 数组不能分割成两个元素和相等的子集.

#### 思路：  
- 背包的体积为sum / 2  
- 背包要放入的商品（集合里的元素）重量为 元素的数值，价值也为元素的数值  
- 背包如果正好装满，说明找到了总和为 sum / 2 的子集。  
- 背包中每一个元素是不可重复放入。（01背包）
#### 背包思路：
1. dp[j]表示，容量为j的背包，背包里子集总和为dp[j]
2. 递推公式：dp[j] = Math.max(dp[j],dp[j-nums[i]] + nums[i])
3. 初始化：dp[0] = 0;
4. 遍历顺序，先元素再背包
```
class Solution {
    public boolean canPartition(int[] nums) {
        int[] dp = new int [10001];
        int sum = 0;

        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];
        }

        if (sum % 2 == 1) { //  如果总和是奇数，肯定不能均分
            return false;
        }
        int target = sum / 2;

        for (int i = 0; i < nums.length; i++) {  // 先元素
            for (int j = target; j >= nums[i]; j--) { // 倒序背包
                dp[j] = Math.max(dp[j],dp[j-nums[i]] + nums[i]);
            }
        }

        if (dp[target] == target) {
            return true;
        }else {
            return false;
        }
    }
}
```
[最后一块石头的重量|| leetcode1049](https://leetcode-cn.com/problems/last-stone-weight-ii/)  
#### 思路：
尽量把石头分成重量相同的两堆，相撞完后，剩下的石头最小  
把所有的石头分成target = sum / 2 和 sum - dp[target]  
相撞之后剩下的最小的石头重量为(sum - dp[target] - dp[target])
#### 五步
1. 定义：dp[j] 表示j重量的背包最多能背dp[j]重量的石头
2. 递推公式：dp[j] = Math.max(dp[j],dp[j-stones[i]] + stones[i])
3. 初始化：最大容量就是所有的石头的重量和30X1000；dp[0] = 0;
4. 遍历方式：先石头后背包
```
class Solution {
    public int lastStoneWeightII(int[] stones) {
        int[] dp = new int[1501];
        int sum = 0;

        for (int i : stones) {
            sum += i;
        }
        int target = sum /2;

        for (int i = 0; i < stones.length; i++) {
            for (int j = target; j >= stones[i]; j--) {
                dp[j] = Math.max(dp[j], dp[j-stones[i]] + stones[i]);
            }
        }

        return (sum - dp[target]) - dp[target];
    }
}
```

