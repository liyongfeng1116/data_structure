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

