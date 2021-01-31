## 题目：用 add first 或 add last 这套新的 API 改写 Deque 的代码 ##
```
Deque<String> deque = new ArrayDeque<>();
deque.addFirst("a");
deque.addLast("b");
deque.addLast("c");
System.out.println(deque);

String str = deque.peek();
System.out.println(str);
System.out.println(deque);
while (!deque.isEmpty()){
    System.out.println(deque.poll());
}
System.out.println(deque);
```
## 题目：删除排序数组中的重复项（Facebook、字节跳动、微软在半年内面试中考过） ##
```
//关键点：排序数组
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums.length == 0) {
            return 0;
        }
        //不重复数组的index,下标0的元素一定不重复
        int i = 0;
        for (int j = 1; j < nums.length; j++) {
            if (nums[i] != nums[j]) {
                //不相同即可把不重复数组的index:i+1,然后赋值
                i++;
                nums[i] = nums[j];
            }
        }
        return i+1;
    }
}
```

## 题目：旋转数组（微软、亚马逊、PayPal 在半年内面试中考过） ##
```
//翻转法
class Solution {
    public void rotate(int[] nums, int k) {
        k %= nums.length;
        reverse(nums, 0, nums.length - 1);
        reverse(nums, 0, k - 1);
        reverse(nums, k, nums.length - 1);
    }

    private void reverse(int[] nums, int start, int end) {
        while (start < end) {
            int temp = nums[start];
            nums[start] = nums[end];
            nums[end] = temp;
            start++;
            end--;
        }
    }
}
```

## 题目：合并两个有序链表（亚马逊、字节跳动在半年内面试常考）##
```
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if(l1 == null) {
            return l2;
        }else if(l2 == null) {
            return l1;
        }else if(l1.val < l2.val) {
            l1.next =  mergeTwoLists(l1.next, l2);
            return l1;
        }else{
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
}
```

## 题目：合并两个有序数组（Facebook 在半年内面试常考） ##
```
//思路就是从右往左(从大到小)放
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
		//结果数组右下标
        int resultRight = m + n - 1;
		//m长度的nums1的右下标
        int right1 = m - 1;
		//n长度的num2的右下标
        int right2 = n - 1;
        while (right1 >= 0 || right2 >= 0) {
            if (right1 < 0) {
                nums1[resultRight--] = nums2[right2--];
            } else if (right2 < 0) {
                nums1[resultRight--] = nums1[right1--];
            } else if (nums1[right1] > nums2[right2]) {
                nums1[resultRight--] = nums1[right1--];
            } else {
                nums1[resultRight--] = nums2[right2--];
            }
        }
    }
}
```
## 题目：两数之和（亚马逊、字节跳动、谷歌、Facebook、苹果、微软在半年内面试中高频常考） ##
```
//利用哈希表减少一层循环
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(target - nums[i])) {
                return new int[]{map.get(target - nums[i]), i};
            } else {
                map.put(nums[i], i);
            }
        }
        return new int[2];
    }
}
```
## 移动零（Facebook、亚马逊、苹果在半年内面试中考过） ##
```
class Solution {
    public void moveZeroes(int[] nums) {
        int j = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
                j++;
            }
        }
    }
}
```
## 设计循环双端队列（Facebook 在 1 年内面试中考过） ##
```
class MyCircularDeque {

    int[] data;

    int capacity = 0;

    int count = 0;

    /** Initialize your data structure here. Set the size of the deque to be k. */
    public MyCircularDeque(int k) {
        data = new int[k];
        capacity = k;
    }
    
    /** Adds an item at the front of Deque. Return true if the operation is successful. */
    public boolean insertFront(int value) {
        if((count+1) > capacity){
            return false;
        }
        System.arraycopy(data,0,data,1,count);
        data[0] = value;
        count++;
        return true;
    }
    
    /** Adds an item at the rear of Deque. Return true if the operation is successful. */
    public boolean insertLast(int value) {
        if((count+1) > capacity){
            return false;
        }
        data[count] = value;
        count++;
        return true;
    }
    
    /** Deletes an item from the front of Deque. Return true if the operation is successful. */
    public boolean deleteFront() {
        if(count > 1){
            System.arraycopy(data,1,data,0,count-1);
        }
        if(count>0){
            data[count-1] = -1;
            count--;
            return true;
        }else{
            return false;
        }  
    }
    
    /** Deletes an item from the rear of Deque. Return true if the operation is successful. */
    public boolean deleteLast() {
        if(count > 0){
            data[count-1] = -1;
            count--;
            return true;
        }else{
            return false;
        }
    }
    
    /** Get the front item from the deque. */
    public int getFront() {
        return data[0];
    }
    
    /** Get the last item from the deque. */
    public int getRear() {
        if(count == 0){
            return -1;
        }
        return data[count-1];
    }
    
    /** Checks whether the circular deque is empty or not. */
    public boolean isEmpty() {
        return count == 0;
    }
    
    /** Checks whether the circular deque is full or not. */
    public boolean isFull() {
        return count == capacity;
    }
}
```