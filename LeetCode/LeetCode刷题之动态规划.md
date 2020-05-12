# LeetCode刷题之动态规划

## 1.不同二叉搜索树

   **题目描述**：给定一个整数n，生成所有由1...n为节点所组成的**二叉搜索树**。

​	示例:

​	输入: 3
​	输出:
​	[
  	[1,null,3,2],
  	[3,2,null,1],
  	[3,1,null,null,2],
  	[2,1,3],
  	[1,null,2,null,3]
​	]
​	解释:
​	以上的输出对应以下 5 种不同结构的二叉搜索树：

   	1         3     3      2      1
    	\       /     /      / \      \
     	3     2     1      1   3      2
    	/     /       \                 \
   	2     1         2                 3



​	**C++版：**

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<TreeNode*> helper(int start,int end){
        vector<TreeNode*> res;
        if(start > end){
            res.push_back(NULL);
        }
        for(int i = start;i <= end;i++){
            vector<TreeNode*> lefts = helper(start,i-1);
            vector<TreeNode*> rights = helper(i+1,end);
            for(auto l : lefts){
                for(auto r : rights){
                    TreeNode *root = new TreeNode(i);
                    root->left = l;
                    root->right = r;
                    res.push_back(root);
                }
            }
        }
        return res;
    }
    vector<TreeNode*> generateTrees(int n) {
        vector<TreeNode*> res;
        if(n==0){
            return res;
        }
        res = helper(1,n);
        return res;
    }
};
```

​	**python版：**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def helper(self,start,end):
        res = list()
        if start > end:
            res.append(None)
        for i in range(start,end+1):
            lefts = self.helper(start,i-1)
            rights = self.helper(i+1,end)
            for l in lefts:
                for r in rights:
                    root = TreeNode(i)
                    root.left = l
                    root.right = r
                    res.append(root)
        return res
    def generateTrees(self, n: int) -> List[TreeNode]:
        res = []
        if n == 0:
            return res
        res = self.helper(1,n)
        return res
```

## 2.不同的二叉搜索树2

**题目描述：**给定一个整数 *n*，求以 1 ... *n* 为节点组成的二叉搜索树有多少种？

**示例:**

```
输入: 3
输出: 5
解释:
给定 n = 3, 一共有 5 种不同结构的二叉搜索树:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

**C++版：**

```cpp

//动态规划方法
class Solution {
public:
    int numTrees(int n) {
        vector<int> res(n+1);
        res[0] = 1;
        res[1] = 1;
        for(int i = 2;i <= n;i++){
            for(int j = 1;j <= i;j++){
                res[i] += res[j - 1] * res[i - j];
            }
        }
        return res[n];
    }
};


//卡特兰数 c0 = 1.c1 = 0,cn+1 = (2*(2*n + 1)/n + 2) * cn
class Solution {
public:
    int numTrees(int n) {
        long c = 1;
        for(int i = 0;i<n;i++){
            c = c * 2 * (2 * i + 1) / (i + 2);
        }
        return (int)c;
    }
};
```

**python版：**

```python
//动态规划
class Solution:
    def numTrees(self, n: int) -> int:
        res = [0 for _ in range(n+1)]

        res[0] = 1
        res[1] = 1
        for i in range(2,n+1):
            for j in range(1,i+1):
                res[i] += res[j - 1] * res[i - j]
        return res[n]
    
    
//卡特兰数
class Solution:
    def numTrees(self, n: int) -> int:
        res = 1
        for i in range(n):
            res = res * 2 * (2 * i + 1) // (i + 2)
        return res
```

