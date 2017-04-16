---
layout: post
title:  "Reconstruct BST with Postorder"
date:   2017-04-04 
categories:  Tree, Reconstruction
---

**Description**  
Given the postorder traversal sequence of a binary search tree, reconstruct the original tree.

**Solution**  
一般来说reconstruct一棵binary tree需要给两种遍历顺序的结果，一组结果用于确定root，另一组结果用于划分左右子树进行进一步递归构造。这道题目却只有一组postorder输入，之所以可解因为它对应的是binary search tree， 满足每个结果的左子树小于root，而root又小于右子树的性质。
此题不难想到O(nlogn)的解法，root根据postorder性质总是当前postorder的最后一个元素，然后依据BST的性质找到左右子树的分界点，这一步平均花费O(n/2)。
但此题还有O(n)的解法，优化点就是不再花费O(n/2)去找左右子树的分界点，递归时按照postorder array从右往左的顺序先递归右子树，再递归左子树。如何知道一棵子树构造完成了？递归时往下传递这棵子树的左边界，当发现当前位置的值小于这左边界时停止递归。当然停止递归另一个条件是数组全部遍历结束。因此递归函数的signature是TreeNode recur(int[] postorder, int[] end, int min), 这里min就是当前子树的左边界，end只有一个元素，表示当前postorder当前遍历到的位置或者说未遍历的postorder的最后一个位置。 reconstruct binary tree with preorder & inorder时除了使用inorderMap外也可套用这个思路获得O(n)的解法。
本题代码如下：
```java
  public TreeNode reconstruct(int[] post) {
    return recur(post, new int[]{post.length - 1}, Integer.MIN_VALUE);
  }

  private TreeNode recur(int[] post, int[] end, int min) {
    if (end[0] < 0 || post[end[0]] < min) {
      return null;
    }
    TreeNode root = new TreeNode(post[end[0]--]);
    root.right = recur(post, end, root.key);
    root.left = recur(post, end, min);
    return root;
  }
```
