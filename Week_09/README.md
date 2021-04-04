#### LRU缓存机制

##### 题目：

链接：https://leetcode-cn.com/problems/lru-cache/#/

运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制 。
实现 LRUCache 类：

LRUCache(int capacity) 以正整数作为容量 capacity 初始化 LRU 缓存
int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
void put(int key, int value) 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。


进阶：你是否可以在 O(1) 时间复杂度内完成这两种操作？

示例：

输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

##### 思路：

LRU，Least Recently Used，在java里面可以基于LinkedHashMap进行实现，因为LinkedHashMap是可以保证元素顺序的，其内部也是使用了双向链表进行记录，根据源码注释可以看到，accessOrder为true时为访问顺序有序，false时为插入顺序有序，默认为false。

```java
/**
* The iteration ordering method for this linked hash map: <tt>true</tt>
* for access-order, <tt>false</tt> for insertion-order.
*
* @serial
*/
final boolean accessOrder;
```

此外我们还需要覆盖一个方法removeEldestEntry，根据源码解析：Returns <tt>true</tt> if this map should remove its eldest entry，也就是return true将会删除最年长的元素，在LinkedHashMap里是return false，也就是不删除，但是根据题意我们需要在超出容量时进行删除。

如果我们不使用LinkedHashMap，我们也是通过哈希表加链表进行实现，使得题目要求的两种操作时间复杂度为O(1)

##### 题解：

基于LinkedHashMap实现LRU

```java
class LRUCache extends LinkedHashMap<Integer, Integer>{

    private int capacity;

    public LRUCache(int capacity) {
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }
    
    public int get(int key) {
        return super.getOrDefault(key, -1);
    }
    
    public void put(int key, int value) {
        super.put(key, value);
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity;
    }

}
```

哈希表加链表实现LRU

```java
class LRUCache {

    class DLinkedNode {
        private int key;
        private int value;
        private DLinkedNode prev;
        private DLinkedNode next;
        public DLinkedNode() {}
        public DLinkedNode(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private Map<Integer, DLinkedNode> cache = new HashMap<>();
    private int size;
    private int capacity;
    DLinkedNode head;
    DLinkedNode tail;

    public LRUCache(int capacity) {
        this.size = 0;
        this.capacity = capacity;
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head.next = tail;
        tail.prev = head;
    }
    
    public int get(int key) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            return -1;
        }
        moveToHead(node);
        return node.value;
    }
    
    public void put(int key, int value) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            DLinkedNode newNode = new DLinkedNode(key, value);
            addToHead(newNode);
            cache.put(key, newNode);
            size++;
            if (size > capacity) {
                DLinkedNode tail = removeTail();
                cache.remove(tail.key);
                size--;
            }
        } else {
            node.value = value;
            moveToHead(node);
        }
    }


    private void addToHead(DLinkedNode node) {
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }

    private void removeNode(DLinkedNode node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void moveToHead(DLinkedNode node) {
        removeNode(node);
        addToHead(node);
    }

    private DLinkedNode removeTail() {
        DLinkedNode res = tail.prev;
        removeNode(res);
        return res;
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

示例 1：

输入：s = "12"
输出：2
解释：它可以解码为 "AB"（1 2）或者 "L"（12）。

##### 思路：

动态规划，其推导方程为：

```java
dp[i]含义从下标0到i+1的字符串的解码方式
dp[0] = 1

dp方程：
dp[i] = 0 条件：i == 1 && chars[i - 1] == '0'//前导0情况
dp[i] = 0 条件：i > 1 && chars[i - 1] == '0' && chars[i - 2] ！= '1' & chars[i - 2] != '2'//不能匹配

dp[i] = dp[i - 2] 条件：chars[i - 1] == '0' && chars[i - 2] >= '1' && chars[i - 2] <= '2'//只能按两位解析

dp[i] = dp[i - 1] + d[i - 2] 条件：i > 1 && chars[i - 2] == '1' || (chars[i - 2] == '2' && chars[i - 1] >= '1' && chars[i - 1] <= '6')  //既可以按一位也可以按两位解析
```

##### 题解：

```java
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

