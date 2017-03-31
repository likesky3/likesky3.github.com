---
layout: post
title:  "delete a node in BST"
date:   2017-03-30 
categories: Tree 
---

删除节点是BST基本操作中的难点。解决思路是先找到待删除节点，然后分类讨论待删除节点的位置，各个击破。  
- case1: 节点是叶节点，此时直接删除即可。
- case2: 节点有左子树或者右子树，这时使用其左孩子或者右孩子顶替原节点即可。
- case3: 同时含有左右子树，此时应找到右子树中的最小值代替root，然后从右子树中删除最小值节点。实现时分两种情况是出于方便coding考虑。
这题对我来说之所以难，是因为我一开始把找到待删除节点和实际删除操作混在一起考虑了。不管解决什么问题，一旦眉毛胡子一把抓就变得棘手，正确的解题姿势是将复杂的问题拆解为相对容易些的若干小问题，小问题可能是并列的关系比如绝大多数DP，也可能是串行或者说分层的（类似此题），不管哪种情况要界定好问题的分界，各个击破。  


```java
/**
 * public class TreeNode {
 *   public int key;
 *   public TreeNode left;
 *   public TreeNode right;
 *   public TreeNode(int key) {
 *     this.key = key;
 *   }
 * }
 */
public class Solution {
  public TreeNode delete(TreeNode root, int key) {
    if (root == null) {
      return null;
    }
    // search key in the tree
    if (key < root.key) {
      root.left = delete(root.left, key);
      return root;
    } else if (key > root.key) {
      root.right = delete(root.right, key);
      return root;
    }
    // guarentee root != null && key == root.key
    // case 1 & 2, no children or only has one child
    if (root.left == null) {
      return root.right;
    } else if (root.right == null) {
      return root.left;
    }

    // case 3.1, root.right has no left subtree
    if (root.right.left == null) {
      root.right.left = root.left;
      return root.right;
    }

    // case 3.2, root.right has left subtree, find smallest node in it
    TreeNode smallest = deleteSmallest(root.right);
    smallest.left = root.left;
    smallest.right = root.right;
    return smallest;
  }

  private TreeNode deleteSmallest(TreeNode curr) {
    // go along the most left path to find the smallest node
    TreeNode prev = null;
    while (curr.left != null) {
      prev = curr;
      curr = curr.left;
    }
    prev.left = curr.right;
    return curr;
  }
}
```
