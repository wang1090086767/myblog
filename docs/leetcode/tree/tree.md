# 树类算法的学习记录

## 树的结构

~~~ java
//Definition for a binary tree node.
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode() {}
    TreeNode(int val) { this.val = val; }
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
~~~

## 树的遍历

~~~ java
void traverse(TreeNode root) {
    // 叶子节点边界判断
    if(root == null) return;
    // 前序遍历
    traverse(root.left);
    // 中序遍历
    traverse(root.right);
    // 后序遍历
}
~~~

## Leetcode 题目

### 124题（Hard）, 求二叉树中最大路径和

~~~ java
// 初始结果数为最小数
int ans = Integer.MIN_VALUE;
int oneSideMax(TreeNode root) {
    // 边界判断
    if(root == null) return 0;
    // 获取左侧的最大路径
    int left = max(0, oneSideMax(root.left));
    // 获取右侧的最大路径
    int right = max(0, oneSideMax(root.right));
    // 更新结果（待分析）
    ans = max(ans, left + right + root.val);
    // 返回本层的局部最优
    return max(left, right) + root.val;
}
~~~

### 105 题（中等），根据前序遍历和中序遍历的结果还原一颗二叉树

~~~ java
   /**
     * @param preorder 先序数列
     * @param preStart 先序 开始下标
     * @param preEnd   先序 结束下标
     * @param inorder  中序数列
     * @param inStart  中序开始下标
     * @param inEnd    中序 结束下标
     * @param inMap    中序下标映射
     * @return
     */
    public TreeNode buildTree(int[] preorder, int preStart, int preEnd, int[] inorder, int inStart, int inEnd, Map<Integer, Integer> inMap) {

        /**
         * 边界处理
         */
        if (preStart > preEnd || inStart > inEnd) return null;

        /**
         * 创建树节点
         */
        TreeNode root = new TreeNode(preorder[preStart]);
        // 获取该树节点在中序遍历中的位置
        int inRoot = inMap.get(root.val);
        // 根据中序序列获取左侧子树节点的数量
        int numsLeft = inRoot - inStart;
        // 创建树左子节点,先序序列的区间为[preStart + 1, preStart + numsLeft]
        root.left = buildTree(preorder, preStart + 1, preStart + numsLeft,
                inorder, inStart, inRoot - 1, inMap);
        // 创建数右子节点，先序序列的区间为[preStart + numsLeft + 1, preEnd]
        root.right = buildTree(preorder, preStart + numsLeft + 1, preEnd,
                inorder, inRoot + 1, inEnd, inMap);

        return root;
    }
~~~

### 99题（中等） 恢复一颗 BST

~~~ java

class Solution {
    
    TreeNode pre = null;
    TreeNode x = null;
    TreeNode y = null;
    public void recoverTree(TreeNode root) {
      traverse(root);
      // 交换值
      int t = x.val;
      x.val = y.val;
      y.val = t;

    }

    public void traverse(TreeNode node) {
        if(node == null) return;
        traverse(node.left);
        // 中序遍历找到 恰好 两个节点的值被错误地交换。
        if(pre != null && node.val < pre.val) {
            x = (x == null) ? pre : x;
            y = node;
        }
        pre = node;
        traverse(node.right);
    }
}
~~~
