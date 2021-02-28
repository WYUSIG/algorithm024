#### 柠檬水找零

##### 题目：

链接：https://leetcode-cn.com/problems/lemonade-change

在柠檬水摊上，每一杯柠檬水的售价为 5 美元。

顾客排队购买你的产品，（按账单 bills 支付的顺序）一次购买一杯。

每位顾客只买一杯柠檬水，然后向你付 5 美元、10 美元或 20 美元。你必须给每个顾客正确找零，也就是说净交易是每位顾客向你支付 5 美元。

注意，一开始你手头没有任何零钱。

如果你能给每位顾客正确找零，返回 true ，否则返回 false 。

##### 思路：

因为只有三种面值5、10、20，而且成倍数关系，10能找开的，5肯定也可以(数量足够的话)，因此尽可能使用10的找零，然后再考虑5，也就是贪心算法

##### 题解：

```
class Solution {
    public boolean lemonadeChange(int[] bills) {
        int five = 0, ten = 0;
        for (int bill : bills) {
            if (bill == 5) {
                five++;
            } else if (bill == 10) {
                if (five == 0) {
                    return false;
                }
                five--;
                ten++;
            } else {
                if (five > 0 && ten > 0) {
                    five--;
                    ten--;
                } else if (five >= 3) {
                    five -= 3;
                } else {
                    return false;
                }
            }
        }
        return true;
    }
}
```



#### 买卖股票的最佳时机 II

##### 题目：

链接：https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/description/

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

示例1：

```
输入: [7,1,5,3,6,4]
输出: 7
```

##### 思路：

因为买卖股票都是没有滞后性的，因此我们只需要保持每天收益最优，那么就是全局最优，即贪心算法

##### 题解：

```
class Solution {
    public int maxProfit(int[] prices) {
        int ans = 0;
        int n = prices.length;
        for (int i = 1; i < n; ++i) {
            ans += Math.max(0, prices[i] - prices[i - 1]);
        }
        return ans;
    }
}
```

#### 单词接龙

##### 题目：

链接：https://leetcode-cn.com/problems/word-ladder

字典 wordList 中从单词 beginWord 和 endWord 的 转换序列 是一个按下述规格形成的序列：

序列中第一个单词是 beginWord 。
序列中最后一个单词是 endWord 。
每次转换只能改变一个字母。
转换过程中的中间单词必须是字典 wordList 中的单词。
给你两个单词 beginWord 和 endWord 和一个字典 wordList ，找到从 beginWord 到 endWord 的 最短转换序列 中的 单词数目 。如果不存在这样的转换序列，返回 0。

示例1：

输入：beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
输出：5
解释：一个最短转换序列是 "hit" -> "hot" -> "dot" -> "dog" -> "cog", 返回它的长度 5。

##### 思路：

对于求最短路径的题目，优先考虑广度遍历，因此我们需要先构造一棵树/图，其中以beginWord为根节点，把beginWord与字典里的单词按只需要改变一个字符的就能相互遍历得到的相连。但是如果判断某个单词与其他单词是否只相差1个字符需要两层遍历，我们可以构造出一个虚拟的节点把单词的每个位置依次变成*，那么像hit<-->h\*t<-->hot便会自动相连

##### 题解：

```
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
        int[] dis = new int[nodeNum];
        Arrays.fill(dis, Integer.MAX_VALUE);
        int beginId = wordId.get(beginWord), endId = wordId.get(endWord);
        dis[beginId] = 0;

        Queue<Integer> que = new LinkedList<Integer>();
        que.offer(beginId);
        while (!que.isEmpty()) {
            int x = que.poll();
            if (x == endId) {
                return dis[endId] / 2 + 1;
            }
            for (int it : edge.get(x)) {
                if (dis[it] == Integer.MAX_VALUE) {
                    dis[it] = dis[x] + 1;
                    que.offer(it);
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

#### 单词接龙||

##### 题目：

链接：https://leetcode-cn.com/problems/word-ladder-ii/description/

给定两个单词（beginWord 和 endWord）和一个字典 wordList，找出所有从 beginWord 到 endWord 的最短转换序列。转换需遵循如下规则：

每次转换只能改变一个字母。
转换后得到的单词必须是字典中的单词。
说明:

如果不存在这样的转换序列，返回一个空列表。
所有单词具有相同的长度。
所有单词只由小写字母组成。
字典中不存在重复的单词。
你可以假设 beginWord 和 endWord 是非空的，且二者不相同。
示例 1:

输入:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

输出:
[
  ["hit","hot","dot","dog","cog"],
  ["hit","hot","lot","log","cog"]
]

##### 思路：

这道题跟上面的单词接龙非常像，只是需要输出所有具体最短路径，那么我们广度遍历的时候需要offer进去的是一个List，然后poll出来取最后的元素判断是否与endWord相同，还要注意的是benginWord可能和wordList里面的元素相同，我们添加edge时要注意不要添加重复关系。

##### 题解：

```
class Solution {
  //记录单词和id对应关系的map
  Map<String, Integer> wordId = new HashMap<>();
  //辅助通过id拿到word的数组，数组下标对应单词id
  List<String> idWord = new ArrayList<>();
  //记录单词id之间的关系，二维数组的行下标对应单词id
  List<List<Integer>> edge = new ArrayList<>();
  //单词id，使用时再自增，即可对应数组下标
  int nodeNum = 0;

  public List<List<String>> findLadders(String beginWord, String endWord, List<String> wordList) {
    List<List<String>> res = new ArrayList<>();
    //把单词库里面的都录入wordId、idWord、edge
    for (String word : wordList) {
      addEdge(word);
    }
    //如果起始单词已经在wordId里面则不需要再录入，不然会有重复关系
    if (!wordId.containsKey(beginWord)) {
      addEdge(beginWord);
    }
    //如果endWord不在wordId则代表怎么变换都不可能得到结果，直接返回
    if (!wordId.containsKey(endWord)) {
      return res;
    }
    //先拿出beginWord的id，endWord的id，因为下面都是操作id，edge里面记录的也是id
    int beginId = wordId.get(beginWord), endId = wordId.get(endWord);
    //队列里面存的是走过的路径，如[hit,h*t,hot]
    Queue<List<Integer>> queue = new LinkedList<>();
    //记录每个单词 在这颗以起始单词为根(根的高为0)的高
    int[] dit = new int[nodeNum];
    Arrays.fill(dit,Integer.MAX_VALUE);
    //起始单词为根(根的高为0)
    dit[beginId] = 0;
    //构造第一个路径数组
    List<Integer> tmpBegin = new ArrayList<>();
    //把起始单词放进路径数组
    tmpBegin.add(beginId);
    //加入队列
    queue.offer(tmpBegin);
    while (!queue.isEmpty()) {
      //从队列拿出一个路径数组(队列是FILO)
      List<Integer> x = queue.poll();
      //拿出路径尾部的那个单词id
      int last = x.get(x.size()-1);
      //如果id和endWord的id相等，证明这是一个答案
      if (last == endId) {
        List<String> tmp = new ArrayList<>();
        for (int index : x) {
          //把路径id数组x转换成路径单词数组
          //至于为什么只拿单词的高能整除2的
          //因为以[hit,h*t,hot]为例，中间会有个我们构造出来的不存在的带*的中间态
          if (dit[index] % 2 == 0) {
            tmp.add(idWord.get(index));
          }
        }
        //加入结果集
        res.add(tmp);
      } else {
        //如果路径数组尾步id不等于endWord的id，则遍历它的edge
        for (int it : edge.get(last)) {
          //如果相连的单词id是属于树的下一层或者高还没被赋值过(还没走过这条路径)
          if (dit[it] >= dit[last] + 1) {
            //针对高还没被赋值过(还没走过这条路径)
            dit[it] = dit[last] + 1;
            //注意构造参数x，会复制前面的x路径数组
            List<Integer> tmp = new ArrayList<>(x);
            tmp.add(it);
            //加入队列该路径继续走下去
            queue.offer(tmp);
          }
        }
      }
    }
    return res;
  }

  public void addEdge(String word) {
    addWord(word);
    int id1 = wordId.get(word);
    //因为addWord只是简单录入，还没有算出edge.get(单词id)的关系数组
    char[] array = word.toCharArray();
    //构造带*号的中间态，然后相连，如hit<->h*t<->hot
    for (int i = 0; i < array.length; i++) {
      char temp = array[i];
      array[i] = '*';
      String newWord = new String(array);
      addWord(newWord);
      int id2 = wordId.get(newWord);
      edge.get(id1).add(id2);
      edge.get(id2).add(id1);
      //记得把*号还原成原来的字符
      array[i] = temp;
    }
  }

  public void addWord(String word) {
    //这里很简单，如果没录入过就录入到wordId、idWord、edge
    if (!wordId.containsKey(word)) {
      wordId.put(word, nodeNum++);
      edge.add(new ArrayList<>());
      idWord.add(word);
    }
  }
}
```

#### 岛屿数量

##### 题目：

链接：https://leetcode-cn.com/problems/number-of-islands/

给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

示例 1：

输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1

##### 思路：

这是一道典型dfs的题目，因为涉及往上下左右继续探，只要我们行和列遍历，遇到1就把结果数量+1，然后dfs把该坐标的上下左右及延申的赋值为0，最后返回结果数量

##### 题解：

```
class Solution {
    void dfs(char[][] grid, int r, int c) {
        int nr = grid.length;
        int nc = grid[0].length;

        if (r < 0 || c < 0 || r >= nr || c >= nc || grid[r][c] == '0') {
            return;
        }

        grid[r][c] = '0';
        dfs(grid, r - 1, c);
        dfs(grid, r + 1, c);
        dfs(grid, r, c - 1);
        dfs(grid, r, c + 1);
    }

    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) {
            return 0;
        }

        int nr = grid.length;
        int nc = grid[0].length;
        int num_islands = 0;
        for (int r = 0; r < nr; ++r) {
            for (int c = 0; c < nc; ++c) {
                if (grid[r][c] == '1') {
                    ++num_islands;
                    dfs(grid, r, c);
                }
            }
        }

        return num_islands;
    }
}
```

