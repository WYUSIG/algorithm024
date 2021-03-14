#### 最小路径和

##### 题目：

链接：https://leetcode-cn.com/problems/minimum-path-sum/

给定一个包含非负整数的 `*m* x *n*` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

说明：每次只能向下或者向右移动一步

##### 思路：

找到右下角最小路径和，加入从起点开始计算每个格子的最小路径和，则：

```
i表示行下标，0开始；j表示列下标，0开始。
dp[i][j]含义：(i,j)格子的最小路径和

dp[i][j] = d[i - 1][j] + grid[i][j]; (j == 0)
dp[i][j] = d[i][j - 1] + grid[i][j]; (i == 0)
dp[i][j] = Math.min(dp[i][j - 1], dp[i - 1][j]) + grid[i][j]; (else)
```

然后一直计算到最后一个格子便得出所求最小路径和

##### 题解：

```
class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = grid[0][0];
        for (int i = 1; i < m; i++) {
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        }
        for (int j = 1; j < n; j++) {
            dp[0][j] = dp[0][j - 1] + grid[0][j];
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = Math.min(dp[i][j - 1], dp[i - 1][j]) + grid[i][j];
            }
        }
        return dp[m - 1][n - 1];
    }
}
```



#### 解码方法

##### 题目：

链接：https://leetcode-cn.com/problems/decode-ways/

一条包含字母 A-Z 的消息通过以下映射进行了 编码 ：

'A' -> 1
'B' -> 2
...
'Z' -> 26
要 解码 已编码的消息，所有数字必须基于上述映射的方法，反向映射回字母（可能有多种方法）。例如，"111" 可以将 "1" 中的每个 "1" 映射为 "A" ，从而得到 "AAA" ，或者可以将 "11" 和 "1"（分别为 "K" 和 "A" ）映射为 "KA" 。注意，"06" 不能映射为 "F" ，因为 "6" 和 "06" 不同。

给你一个只含数字的 非空 字符串 num ，请计算并返回 解码 方法的 总数 。

题目数据保证答案肯定是一个 32 位 的整数。

**示例 1：**

```
输入：s = "12"
输出：2
解释：它可以解码为 "AB"（1 2）或者 "L"（12）。
```

##### 思路：

```
dp[i]含义从下标0到i+1的字符串的解码方式
dp[0] = 1

dp方程：
dp[i] = 0 条件：i == 1 && chars[i - 1] == '0'//前导0情况
dp[i] = 0 条件：i > 1 && chars[i - 1] == '0' && chars[i - 2] ！= '1' & chars[i - 2] != '2'//不能匹配

dp[i] = dp[i - 2] 条件：chars[i - 1] == '0' && chars[i - 2] >= '1' && chars[i - 2] <= '2'//只能按两位解析

dp[i] = dp[i - 1] + d[i - 2] 条件：i > 1 && chars[i - 2] == '1' || (chars[i - 2] == '2' && chars[i - 1] >= '1' && chars[i - 1] <= '6')  //既可以按一位也可以按两位解析
```

##### 题解：

```
class Solution {
    //状态定义：dp[i]为第i个位置解码方法的总数
    public int numDecodings(String s) {
        char[] chars = s.toCharArray();
        if (chars[0] == '0') return 0;
        int[] dp = new int[chars.length + 1];
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i <= chars.length; i++) {
            if (chars[i - 1] == '0') {
                //如果前一位不是1或者2,显然无法解码
                if (chars[i - 2] != '1' && chars[i - 2] !='2') return 0;
                //如果前一位是1或者2
                dp[i] = dp[i - 2];
            } else if (chars[i - 2] == '1' || (chars[i - 2] == '2' && chars[i - 1] >= '1' && chars[i - 1] <= '6')) {
                dp[i] = dp[i - 1] + dp[i - 2];
            } else {
                dp[i] = dp[i - 1];
            }
        }
        return dp[chars.length];
    }
}
```



#### 最大正方形

##### 题目：

链接：https://leetcode-cn.com/problems/maximal-square/

在一个由 '0' 和 '1' 组成的二维矩阵内，找到只包含 '1' 的最大正方形，并返回其面积。

示例 1：

输入：matrix = [["1","0","1","0","0"],["1","0","1","1","1"],["1","1","1","1","1"],["1","0","0","1","0"]]
输出：4

##### 思路：

```
i表示行下标，0开始；j表示列下标，0开始。
dp[i][j]含义：(i,j)格子最大正方形边长

dp[i][j] = matrix == 1 ? 1 : 0; (j == 0)
dp[i][j] = matrix == 1 ? 1 : 0; (i == 0)
dp[i][j] = Math.min(Math.min(dp[i][j - 1], dp[i - 1][j]), dp[i - 1, dp[j - 1]]) + 1; (else) //对角，左边，上边三个格子三个都最大正方形至少这么大，才继承+1
```



##### 题解：

```
class Solution {
    public int maximalSquare(char[][] matrix) {
        int maxSide = 0;
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return maxSide;
        }
        int rows = matrix.length, columns = matrix[0].length;
        int[][] dp = new int[rows][columns];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < columns; j++) {
                if (matrix[i][j] == '1') {
                    if (i == 0 || j == 0) {
                        dp[i][j] = 1;
                    } else {
                        dp[i][j] = Math.min(Math.min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
                    }
                    maxSide = Math.max(maxSide, dp[i][j]);
                }
            }
        }
        int maxSquare = maxSide * maxSide;
        return maxSquare;
    }
}
```

