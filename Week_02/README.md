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