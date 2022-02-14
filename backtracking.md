# 回溯算法
    组合问题：N个数里面按一定规则找出k个数的集合
    排列问题：N个数按一定规则全排列，有几种排列方式
    切割问题：一个字符串按一定规则有几种切割方式
    子集问题：一个N个数的集合里有多少符合条件的子集
    棋盘问题：N皇后，解数独等等
## 导航
#### 题目大纲
![题目大纲](./pics/backtracking/题目大纲.png)
1. 回溯算法的问题可以抽象为树形问题——>递归在集合中查找子集，集合的大小构成了树的宽度，递归的深度构成树的深度。
2. (1)递归函数的返回值及参数 （2）回溯函数终止条件 （3）回溯搜索的遍历过程  
![回溯搜索示意图](./pics/backtracking/回溯搜索示意图.png)

## 组合问题
- [组合leetcode77](https://leetcode-cn.com/problems/combinations/)
```
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    void backtracking (int n, int k, int startIndex) { // 参数中包括开始的结点标签
        // 终止条件
        if (path.size() == k) {
            res.add(new ArrayList<>(path));
            return;
        }
        // 横向遍历
        for (int i = startIndex; i <= n; i++) { // 控制横向遍历 
            path.add(i); // 处理结点
            // 纵向递归
            backtracking(n, k, i+1); // 控制树的纵向遍历，注意下一层搜索要从i+1开始
            path.removeLast(); // 撤销本次处理的结点
        }
    }
    public List<List<Integer>> combine(int n, int k) {
        backtracking(n, k, 1);
        return res;
    }
}
```
- [组合总和leetcode216](https://leetcode-cn.com/problems/combination-sum-iii/)
```
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    void backtracking (int n, int k, int startIndex) {
        int sum = 0;
        for (int i = 0; i < path.size(); i++) {
            sum += path.get(i);
        }
        if (sum == n && k == path.size()) {
            res.add(new ArrayList<>(path));
            return;
        }

        for (int i = startIndex; i <= 9; i++) {
            path.add(i);
            backtracking(n, k, i+1);
            path.removeLast();
        }
    }
    public List<List<Integer>> combinationSum3(int k, int n) {
        backtracking(n, k, 1);
        return res;
    }
}
```
- [电话号码的字母组合leetcode17](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)
```
class Solution {
    List<String> res = new ArrayList<>();
    StringBuilder temp = new StringBuilder(); // 注意字符串
    String [] numString = {"","","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
    void backtracking (String digits, int index) { //控制的纵向的递归。index为书的深度也是遍历字母的个数 
        if (index == digits.length()) {
            res.add(temp.toString());
            return;
        }   

        String str = numString[digits.charAt(index) - '0']; // 找到数字对应的字符集
        for (int i = 0; i < str.length(); i++) { // 水平展开
            temp.append(str.charAt(i));
            backtracking(digits, index + 1);
            temp.deleteCharAt(temp.length() - 1);
        }
    }
    public List<String> letterCombinations(String digits) {
        if (digits == null || digits.length() == 0) {
            return res;
        }
        backtracking(digits, 0);
        return res;
    }
}
```
- [组合总和leetcode39](https://leetcode-cn.com/problems/combination-sum/)
```
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    void backtracking (int[] candidates, int target, int startIndex) {
        int sum = 0;
        for (int i = 0; i < path.size(); i++) {
            sum += path.get(i);
        }
        if (sum == target) {
            res.add(new ArrayList<>(path));
            return;
        }
        if (sum > target) {
            return;
        }

        for (int i = startIndex; i < candidates.length; i++) {
            path.add(candidates[i]);
            backtracking(candidates, target, i); // 此处是i，表示可以重复读取当前的的数
            path.removeLast();
        }
    }
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        backtracking(candidates, target, 0);
        return res;
    }
}
```
- [组合问题2](https://leetcode-cn.com/problems/combination-sum-ii/)
```
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    void backtracking (int[] candidates, int target, int index, boolean[] used) {
        int sum = 0;
        for (int i = 0; i < path.size(); i++) {
            sum += path.get(i);
        }
        if (sum == target) {
            res.add(new ArrayList<>(path));
            return;
        }
        if (sum > target) {
            return;
        }

        for (int i = index; i < candidates.length; i++) {
            if (i > 0 && candidates[i] == candidates[i - 1] && used[i - 1] == false) {
                continue;
            }
            used[i] = true;
            path.add(candidates[i]);
            // 每个结点选择一次，所以从下一次开始
            backtracking(candidates, target, i + 1, used);
            used[i] = false;
            path.removeLast();
        }
    }
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        // 把重复的数都放到一起
        Arrays.sort(candidates);
        boolean [] flag = new boolean[candidates.length];
        backtracking(candidates, target, 0, flag);
        return res;
    }
}
```
- [分割回文串leetcode131](https://leetcode-cn.com/problems/palindrome-partitioning/)