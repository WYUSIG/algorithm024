## 有效的字母异位词 ##
思路：排序、哈希表计数

最优解：排序

java API：

String#toCharArray()：可以把String转成char数组

Arrays.sort(基本类型数组,boolean除外)：可以对基本类型数组进行排序

Arrays.equals(基本类型数组，boolean除外，可以是Object数组)：对基本类型数组或对象数组进行排序，对象数组排序里面是调用equals进行比较，如果有特殊要求需要重写hashCode和equals方法
```
class Solution {
    public boolean isAnagram(String s, String t) {
        char[] str1 = s.toCharArray();
        char[] str2 = t.toCharArray();
        Arrays.sort(str1);
        Arrays.sort(str2);
        return Arrays.equals(str1, str2);
    }
}
```
## 两数之和 ##
思路：暴力、哈希表、排序双指针

最优解：哈希表

java HashMap常用API：

- public V put(K key, V value)
- public V get(Object key)
- public V remove(Object key)
- public boolean containsKey(Object key)
- public V getOrDefault(Object key, V defaultValue)
- public Collection<V> values()
- 遍历：for(Map.Entry<K,V> entry : map.entrySet())使用public Set<Map.Entry<K,V>> entrySet()
```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer,Integer> map = new HashMap<>();
        for(int i = 0; i<nums.length; i++) {
            int other = target - nums[i];
            if(map.containsKey(other)) {
                return new int[]{map.get(other), i};
            }else{
                map.put(nums[i], i);
            }
        }
        return new int[2];
    }
}
```
## N 叉树的前序遍历 ##
思路：递归，迭代(栈辅助)

最优解：递归(实测)
```
//递归
class Solution {
    List<Integer> res = new ArrayList<>();
    public List<Integer> preorder(Node root) {
        if(root == null) {
            return res;
        }
        res.add(root.val);
        for(Node node : root.children) {
            preorder(node);
        }
        return res;
    }
}
```
```
//迭代
class Solution {
    public List<Integer> preorder(Node root) {
        LinkedList<Node> stack = new LinkedList<>();
        List<Integer> res = new ArrayList<>();
        if(root == null){
            return res;
        }
        stack.add(root);
        while(!stack.isEmpty()){
            Node curNode = stack.pollLast();
            res.add(curNode.val);
            Collections.reverse(curNode.children);
            for(Node node : curNode.children){
                stack.add(node);
            }
        }
        return res;
    }
}
```
## 字母异位词分组 ##
思路：排序，哈希表

最优解：排序

```
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String,List<String>> map = new HashMap<>();
        for(String str : strs){
            char[] strChar = str.toCharArray();
            Arrays.sort(strChar);
            String key = new String(strChar);
            List<String> list = map.getOrDefault(key,new ArrayList<>());
            list.add(str);
            map.put(key,list);
        } 
        return new ArrayList<>(map.values());
    }
}
```