#### 二叉树的最近公共祖先

思路：递归，深度遍历，在深度遍历递归函数里面如果自己或子树只包含p或q中的一个，那就返回true否则false，如果包含两个则得出结果，并且整颗二叉树最多只有的一个解

注意：p != q而且node.val互不相同

```
class Solution {
    TreeNode ans = null;
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        dfs(root,p,q);
        return ans;
    }
    public boolean dfs(TreeNode root,TreeNode p,TreeNode q){
        if(root == null){
            return false;
        }
        boolean lson = dfs(root.left,p,q);
        boolean rson = dfs(root.right,p,q);
        if((lson && rson) || ((root.val == p.val || root.val == q.val) && (lson || rson))){
            ans = root;
        }
        return lson || rson || (root.val == p.val || root.val == q.val);
    }
}
```

#### 从前序与中序遍历序列构造二叉树

思路：递归，并且由前序遍历和中序遍历拿出整颗二叉树的根节点，开始构造，然后一次对左子树和右子树重复上述操作。

因为该二叉树没有重复元素，所以可以把中序遍历的数组装进哈希表，利用O(1)的时间复杂度拿到根节点的数组下标。

因为前序遍历为：

```
[ 根节点, [左子树的前序遍历结果], [右子树的前序遍历结果] ]
```

中序遍历为：

```
[ [左子树的中序遍历结果], 根节点, [右子树的中序遍历结果] ]
```

所以在重复子问题中：

根节点的中序遍历数组下标为：

```
int inorderRoot  = indexMap.get(preorder[preorderRoot])
```

递归生成左子树和右子树：

左子树长度为：

```
int sizeLeftSubtree = inorderRoot - inorderLeft;
```

左子树的前序遍历和中序遍历数组下标分别为：

```
preorderLeft+1到preorderLeft+sizeLeftSubtree
inorderLeft到inorderRoot-1
```

右子树的前序遍历和中序遍历数组下标分别为：

```
preorderLeft+sizeLeftSubtree+1到preorderRight
inorderRoot+1到inorderRight
```



最后代码为：

```
class Solution {
    
    private Map<Integer,Integer> indexMap = new HashMap<>();

    public TreeNode myBuildTree(int[] preorder,int[] inorder,int preorderLeft,int preorderRight,int inorderLeft,int inorderRight){
        if(preorderLeft > preorderRight){
            return null;
        }
        int preorderRoot = preorderLeft;
        int inorderRoot = indexMap.get(preorder[preorderRoot]);
        TreeNode root = new TreeNode(preorder[preorderRoot]);
        int sizeLeftSubtree = inorderRoot - inorderLeft;
        root.left = myBuildTree(preorder,inorder,preorderLeft+1,preorderLeft+sizeLeftSubtree,inorderLeft,inorderRoot-1);
        root.right = myBuildTree(preorder,inorder,preorderLeft+sizeLeftSubtree+1,preorderRight,inorderRoot+1,inorderRight);
        return root;
    }

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        int n = preorder.length;
        for(int i=0;i<n;i++){
            indexMap.put(inorder[i],i);
        }
        return myBuildTree(preorder,inorder,0,n-1,0,n-1);
    }
}
```

#### 组合

思路：递归，只需要按照递归模板来，需要注意的是进入下一层有两条分支，一个是选择使用当前元素，一个是选择不使用当前元素，最后注意剩余元素不足以生成k长度的子集时直接return

```
class Solution {
    List<Integer> temp = new ArrayList<>();
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> combine(int n, int k) {
        dfs(1,n,k);
        return res;
    }

    public void dfs(int cur,int n,int k){
        if(temp.size()+(n-cur+1) < k){
            return;
        }
        if(temp.size() == k){
            res.add(new ArrayList<>(temp));
            return;
        }
        //选择使用当前元素
        temp.add(cur);
        dfs(cur+1,n,k);
        temp.remove(temp.size()-1);
        //选择不使用当前元素
        dfs(cur+1,n,k);
    }
}
```

#### 全排列

思路：递归

```
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<Integer> output = new ArrayList<>();
        List<List<Integer>> res = new ArrayList<>();
        int n = nums.length;
        for(int num : nums){
            output.add(num);
        }
        backtrack(n,output,res,0);
        return res;
    }

    public void backtrack(int n,List<Integer> output,List<List<Integer>> res,int first){
        if(first == n){
            res.add(new ArrayList<>(output));
            return;
        }
        for(int i=first;i<n;i++){
        	//把nums[i]放到first位置上
            Collections.swap(output,first,i);
            backtrack(n,output,res,first+1);
            //还原
            Collections.swap(output,first,i);
        }
    }
}
```

#### 全排列||

思路：排序+递归，并使用一个数组记录每一位元素是否在该层递归当作参数传递过来，是否已经被选择使用

注意：注意递归还原

```
class Solution {
    boolean[] vis;

    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> ans = new ArrayList<List<Integer>>();
        List<Integer> perm = new ArrayList<Integer>();
        //默认里面都是false
        vis = new boolean[nums.length];
        Arrays.sort(nums);
        backtrack(nums, ans, 0, perm);
        return ans;
    }

    public void backtrack(int[] nums, List<List<Integer>> ans, int idx, List<Integer> perm) {
        if (idx == nums.length) {
            ans.add(new ArrayList<Integer>(perm));
            return;
        }
        for (int i = 0; i < nums.length; ++i) {
        	//如果上一层已经选择了该元素 或者 和前一个元素重复且前一个元素没有被选择，因为如果元素相同且前一个为false的话证明它在上面的递归中已经被选择过然后还原成false了
            if (vis[i] || (i > 0 && nums[i] == nums[i - 1] && !vis[i - 1])) {
                continue;
            }
            perm.add(nums[i]);
            vis[i] = true;
            backtrack(nums, ans, idx + 1, perm);
            vis[i] = false;
            perm.remove(idx);
        }
    }
}
```

