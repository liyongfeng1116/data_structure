# DFS&BFS
## 问题1：[岛屿问题DFS](https://mp.weixin.qq.com/s?__biz=MzA5ODk3ODA4OQ==&mid=2648167208&idx=1&sn=d8118c7c0e0f57ea2bdd8aa4d6ac7ab7&chksm=88aa236ebfddaa78a6183cf6dcf88f82c5ff5efb7f5c55d6844d9104b307862869eb9032bd1f&token=1064083695&lang=zh_CN#rd)
### 问题描述：
岛屿问题其实是遍历问题。0代表海洋，1代表陆地。1的连接代表岛屿。
![岛屿问题](.\pics\DFS&BFS\岛屿问题.jpg )
### 常见问法：
岛屿的数量、面积、周长等。
### 知识点：
DFS:深度优先搜索
1. **二叉树的DFS：**[**访问相邻结点**] 和 [**判断base case**]
访问相邻结点：左子树和右子树
判断base case：`root == null`(1.子树为空，不再遍历；2.及时返回，不因为`root.left`出现空指针)
2. **网格的DFS:**
   1. 相邻结点是四叉结构：**前后左右**
![四叉结构](.\pics\DFS&BFS\四叉结构.jpg)
   2. base case：**格子越界**（类似于 root==null 当下一个为空立即返回）
![base_case](.\pics\DFS&BFS\base_case.jpg)
网格DFS框架
```
void dfs(int[][] grid, int r, int c) {
    // 判断base case,如果左边越界，直接返回
    if (!inArea(grid, r, c)) {
        return;
    }

    // 访问四个结点叉
    dfs(grid,r-1,c);
    dfs(grid,r+1,c);
    dfs(grid,r,c-1);
    dfs(grid,r,c+1);
}
// 判断坐标是否在网格中
boolean inArea(int[][] grid, int r, int c) {
    return 0 <= r && r <= grid.length 
    && 0 <= c && c <= grid[0].length;
}
```
防止重复遍历：
![重复遍历](.\pics\DFS&BFS\重复遍历.gif)
为避免重复遍历，需要标记已经遍历过的点。

- 0——海洋
- 1——陆地（未遍历）
- 2——陆地（已遍历）
```
void dfs(int[][] gird, int r, int c) {
    if (! inArea(grid, r, c)) {
        return;
    }
    // 如果已经走过则直接返回
    if (grid[r,c] != 1) {
        return;
    }
    // 将格子标记为遍历过
    grid[r][c] == 2;

    dfs(grid, r-1, c);
    dfs(grid, r+1, c);
    dfs(grid, r, c-1);
    dfs(grid, r, c+1);
}

boolean(int[][] grid, int r, int c){
    return 0 <= r && r < grid.length
    && 0<= c && c < grid[0].length;
}
```
### 例题
1. [岛屿的最大面积leetcode695](https://leetcode-cn.com/problems/max-area-of-island/)
```
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        int res = 0;
        for (int r = 0; r < grid.length; r++) {
            for (int c = 0; c < grid[0].length; c++) {
                if (grid[r][c] == 1) {
                    int a = area(grid, r, c);
                    res = Math.max(res,a);
                }
            }
        }
        return res;
    }

    public int area(int[][] grid, int r, int c) {
        // 出界立即返回
        if (!inArea(grid, r, c)) {
            return 0;
        }
        // 当已经遍历过，就不重复加
        if (grid[r][c] != 1) {
            return 0;
        }

        grid[r][c] = 2;

        int num = 1;
        num += area(grid, r - 1, c);
        num += area(grid, r + 1, c);
        num += area(grid, r, c - 1);
        num += area(grid, r, c + 1);
        return num;
    }

    public boolean inArea(int[][] grid, int r, int c) {
        return 0 <= r && r < grid.length &&
        0 <= c && c < grid[0].length;
    }
}
```

2. 填海造陆问题
```

```
3. 岛屿周长
```
class Solution {
    public int islandPerimeter(int[][] grid) {
        for (int r = 0; r < grid.length; r++) {
            for (int c = 0; c < grid[0].length; c++) {
                if (grid[r][c] == 1) {
                    // 题目中只有一个陆地，所以随便从一个1开始
                    return dfs(grid, r, c);
                }
            }
        }
        return 0;
    }

    public int dfs(int[][] grid, int r, int c) {
        int res = 0;
        // 越出边界，加1
        if (!inArea(grid, r, c)) {
            return 1;
        }
        //是岛屿，说明越界，不管从哪边越界，均加1
        if (grid[r][c] == 0) {
            return 1;
        }
        // 是已经遍历过的陆地，就跳过
        if (grid[r][c] != 1) {
            return 0;
        }

        grid[r][c] = 2;

        res += dfs(grid, r-1, c);
        res += dfs(grid, r+1, c);
        res += dfs(grid, r, c-1);
        res += dfs(grid, r, c+1);

        return res;
    }

    public boolean inArea(int[][] grid, int r, int c) {
        return 0 <= r && r < grid.length 
        && 0 <= c && c < grid[0].length;
    }
}
```
## 最短路径BFS
- 有权最短路径（Dijkstra算法）
- 无权最短路径（BFS层序遍历）
层序遍历模板
- 层序遍历采用队列
- 每次判断四个方向是否可以遍历（终止条件队列为空）
```
void bfs(int[][] grid, int r, int c) {
    Queue<int[]> queue = new ArrayQueue<>();
    queue.add(new int[]{r, c});
    while (!queue.isEmpty()) {
        int n = queue.size();
        for (int i = 0; i < n; i++) {
            int [] node = queue.poll();
            int r = node[0];
            int c = node[1];
            if (r-1 >= 0 && grid[r-1][c] == 0) {
                grid[r-1][c] = 2;
                queue.add(new int[]{r-1,c});
            }
            if (r+1 < N && grid[r+1][c] == 0) {
                grid[r+1][c] = 2;
                queue.add(new int[]{r+1,c});
            }
            if (c-1 >= 0 && grid[r][c-1] == 0) {
                grid[r][c-1] = 2;
                queue.add(new int[]{r,c-1});
            }
            if (c+1 < N && grid[r][c+1] == 0) {
                grid[r][c+1] = 2;
                queue.add(new int[]{c+1});
            }
        }
    }
}
```
### 岛屿层序遍历
1. [离开陆地的最远距离leetcode1162](https://leetcode-cn.com/problems/as-far-from-land-as-possible)
```
class Solution {
    public int maxDistance(int[][] grid) {
        int N = grid.length;
        Queue<int[]> queue = new ArrayDeque<>();
        // 把所有的陆地加入到队列中（横纵坐标）
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                if (grid[i][j] == 1) {
                    queue.add(new int[]{i,j});
                }
            }
        }
        // 全为岛屿或者全为海洋，返回-1
        if (queue.isEmpty() || queue.size() == N*N) {
            return -1;
        }
        // 移动的方向
        int[][] moves = {
            {-1,0},{1,0},{0,-1},{0,1},
        };
        int distance = -1; //每过一层，距离就增加1
        while (!queue.isEmpty()) {
            distance++;
            int n = queue.size();
            for (int i = 0; i < n; i++) {
                int[] node = queue.poll();
                int r = node[0];
                int c = node[1];
                for (int[] move : moves) {
                    int r2 = r + move[0];
                    int c2 = r + move[1];
                    if (inArea(grid,r2,c2) && grid[r2][c2] == 0) {
                        grid[r2][c2] = 2;
                        queue.add(new int[]{r2,c2});
                    }
                }
            }   
        }
        return distance;
    }
    public boolean inArea(int[][] grid, int r, int c){
        return 0 <= r && r < grid.length
        && 0 <= c && c < grid[0].length;
    }
}
```
2. [01矩阵leetcode542](https://leetcode-cn.com/problems/01-matrix/)
```
class Solution {
    public int[][] updateMatrix(int[][] mat) {
        // 转换思维，0的距离就是0，因此实际上是1和哪个0最近，从多个0开始扩散
        int N1 = mat.length;
        int N2 = mat[0].length;
        Queue<int[]> queue = new ArrayDeque<>();
        for (int i = 0; i < N1; i++) {
            for (int j = 0; j < N2; j++) {
                if (mat[i][j] == 0) {   //入栈0
                    queue.offer(new int[]{i,j});
                } else {    // 标记1的位置没有被访问过
                    mat[i][j] = -1;
                }
            }
        }

        int [][] moves = {{-1,0},{1,0},{0,-1},{0,1}};
        while (!queue.isEmpty()) {
            int [] node = queue.poll();
            int r = node[0];
            int c = node[1];
            for (int[] move : moves) {
                int r1 = r + move[0];
                int c1 = c + move[1];
                if (inArea(mat,r1,c1) && mat[r1][c1] == -1) { //未越界，且找到1，就等于之前的+1
                    mat[r1][c1] = mat[r][c] + 1;
                    queue.offer(new int[]{r1,c1});              
                }  
            }
        }
        return mat;
    }
    public boolean inArea(int[][] mat, int r, int c){
        return r >= 0 && r < mat.length 
        && c >= 0 && c < mat[0].length;
    }
}
```
3. [腐烂的橘子leetcode994](https://leetcode-cn.com/problems/rotting-oranges/)
```
class Solution {
    public int orangesRotting(int[][] grid) {
        Queue<int[]> queue = new LinkedList<>();
        int count = 0;
        int N = grid.length;
        int M = grid[0].length;
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < M; j++) {
                if (grid[i][j] == 2) {
                    queue.offer(new int[]{i,j});
                }else if (grid[i][j] == 1) {
                    count++;
                }    
            }
        }

        int[][] moves = {{-1,0},{1,0},{0,-1},{0,1}}; 
        int round = 0;

        while (count > 0 && !queue.isEmpty()) {
            round++;
            int n = queue.size();
            for (int i = 0; i < n; i++) {
                int [] node = queue.poll();
                int r = node[0];
                int c = node[1];
                for (int[] move : moves) {
                    int r1 = r + move[0];
                    int c1 = c + move[1];
                    if (inArea(grid, r1, c1) && grid[r1][c1] == 1) {
                        grid[r1][c1] = 2;
                        count--;
                        queue.add(new int[] {r1,c1});
                    }
                }
            }  
        }
        
        if (count > 0) {
            return -1;
        }else{
            return round;
        }
    }

    public boolean inArea(int[][] grid, int r, int c) {
        return r >= 0 && r < grid.length 
        && c >= 0 && c < grid[0].length;
    }
}
```



