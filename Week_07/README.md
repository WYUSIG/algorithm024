#### 爬楼梯

##### 题目：

链接：https://leetcode-cn.com/problems/climbing-stairs/

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。

示例 1：

输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶

##### 思路：

动态规划，递推方程：

```
dp[i] = 1; (i == 1 || i == 0)
dp[i] = dp[i - 1] + dp[i - 2]; (i > 1)
```

##### 题解：

```java
class Solution {
    public int climbStairs(int n) {
        int[] dp = new int[n + 1];
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
}
```



#### 括号生成

##### 题目：

链接：https://leetcode-cn.com/problems/generate-parentheses/

数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。

示例 1：

输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
示例 2：

输入：n = 1
输出：["()"]

##### 思路：

使用深度遍历，并记录左右括号使用情况

只需要左括号还没使用完，就可以尝试添加左括号

只有右括号的使用的个数小于左括号使用的个数，才可以添加右括号(剪枝)

##### 题解：

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        dfs(res, n, n, "");
        return res;
    }

    public void dfs(List<String> res,int left,int right,String temp){
        if(left == 0 && right == 0) {
            res.add(temp);
            return;
        }
        if(left > 0) {
            dfs(res, left-1, right, temp+"(");
        }
        if(left < right) {
            dfs(res, left, right-1, temp+")");
        }
    }
}
```



#### 单词接龙

##### 题目：

链接：https://leetcode-cn.com/problems/word-ladder/

字典 wordList 中从单词 beginWord 和 endWord 的 转换序列 是一个按下述规格形成的序列：

序列中第一个单词是 beginWord 。
序列中最后一个单词是 endWord 。
每次转换只能改变一个字母。
转换过程中的中间单词必须是字典 wordList 中的单词。
给你两个单词 beginWord 和 endWord 和一个字典 wordList ，找到从 beginWord 到 endWord 的 最短转换序列 中的 单词数目 。如果不存在这样的转换序列，返回 0。


示例 1：

输入：beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
输出：5
解释：一个最短转换序列是 "hit" -> "hot" -> "dot" -> "dog" -> "cog", 返回它的长度 5。
示例 2：

输入：beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log"]
输出：0
解释：endWord "cog" 不在字典中，所以无法进行转换。

##### 思路：

对于求最短路径的题目，优先考虑广度遍历，因此我们需要先构造一棵树/图，其中以beginWord为根节点，把beginWord与字典里的单词按只需要改变一个字符的就能相互遍历得到的相连。但是如果判断某个单词与其他单词是否只相差1个字符需要两层遍历，我们可以构造出一个虚拟的节点把单词的每个位置依次变成*，那么像hit<-->h\*t<-->hot便会自动相连

同时为了优化，可以使用双向广度遍历，一边从 beginWord开始，另一边从 endWord开始。我们每次从两边各扩展一层节点，当发现某一时刻两边都访问过同一顶点时就停止搜索。

##### 题解：

```java
class Solution {
    Map<String, Integer> wordId = new HashMap<String, Integer>();
    List<List<Integer>> edge = new ArrayList<List<Integer>>();
    int nodeNum = 0;

    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        for (String word : wordList) {
            addEdge(word);
        }
        addEdge(beginWord);
        if (!wordId.containsKey(endWord)) {
            return 0;
        }

        int[] disBegin = new int[nodeNum];
        Arrays.fill(disBegin, Integer.MAX_VALUE);
        int beginId = wordId.get(beginWord);
        disBegin[beginId] = 0;
        Queue<Integer> queBegin = new LinkedList<Integer>();
        queBegin.offer(beginId);
        
        int[] disEnd = new int[nodeNum];
        Arrays.fill(disEnd, Integer.MAX_VALUE);
        int endId = wordId.get(endWord);
        disEnd[endId] = 0;
        Queue<Integer> queEnd = new LinkedList<Integer>();
        queEnd.offer(endId);

        while (!queBegin.isEmpty() && !queEnd.isEmpty()) {
            int queBeginSize = queBegin.size();
            for (int i = 0; i < queBeginSize; ++i) {
                int nodeBegin = queBegin.poll();
                if (disEnd[nodeBegin] != Integer.MAX_VALUE) {
                    return (disBegin[nodeBegin] + disEnd[nodeBegin]) / 2 + 1;
                }
                for (int it : edge.get(nodeBegin)) {
                    if (disBegin[it] == Integer.MAX_VALUE) {
                        disBegin[it] = disBegin[nodeBegin] + 1;
                        queBegin.offer(it);
                    }
                }
            }

            int queEndSize = queEnd.size();
            for (int i = 0; i < queEndSize; ++i) {
                int nodeEnd = queEnd.poll();
                if (disBegin[nodeEnd] != Integer.MAX_VALUE) {
                    return (disBegin[nodeEnd] + disEnd[nodeEnd]) / 2 + 1;
                }
                for (int it : edge.get(nodeEnd)) {
                    if (disEnd[it] == Integer.MAX_VALUE) {
                        disEnd[it] = disEnd[nodeEnd] + 1;
                        queEnd.offer(it);
                    }
                }
            }
        }
        return 0;
    }

    public void addEdge(String word) {
        addWord(word);
        int id1 = wordId.get(word);
        char[] array = word.toCharArray();
        int length = array.length;
        for (int i = 0; i < length; ++i) {
            char tmp = array[i];
            array[i] = '*';
            String newWord = new String(array);
            addWord(newWord);
            int id2 = wordId.get(newWord);
            edge.get(id1).add(id2);
            edge.get(id2).add(id1);
            array[i] = tmp;
        }
    }

    public void addWord(String word) {
        if (!wordId.containsKey(word)) {
            wordId.put(word, nodeNum++);
            edge.add(new ArrayList<Integer>());
        }
    }
}
```

