---
layout: post
title:  "Reconstruct Binary Tree with Levelorder and Inorder"
date:   2017-04-04
categories:  Tree, Recursion 
---


这类题目的解题思路都挺类似的，**先找到root节点，然后somehow将input拆分为左右子树的输入，然后递归地构造出左右子树**。此题的难点是如何将levelOrder对应的数组拆分为左右子树的levelOrder数组。回想下通过两组遍历顺序结果恢复对应binary tree的题目的input中均包含inorder遍历结果，这并不是偶然，因为在这类题目中我们总是通过inorder数组来拆分input给左右子树，这是inorder遍历结果的性质使然。一旦我们知道root，遍历inorder结果找到root所在位置，则其左边为left subtree结果，右边为right subtree的inorder结果。
回到此题，levelOrder遍历，第一个一定是root，确定root后可通过inorder结果知道left subtree包含哪些元素，然后据此去split levelOrder。遍历levelOrder剩余元素，属于left subtree的元素收集到一起（加入一个list或者紧挨放置在数组的一头），不属于left subtree也即属于right subtree的元素也收集放在一起，如此便得到了左右子树递归的input。
该题实现上有个优化点需要特别指出来，如果通过inorder来split levelOrder，high level的思想就是在inorder是按照left-subtree, root, right-subtree的顺序排列的，所以我们在最开始根据inorder array创建出一个inorder map, key是元素值，value是该元素在inorder array中的index。这样就可以O(1)判断出一个元素是属于left subtree还是right subtree。
下面的实现Time Complexity: O(nlogn), Space Complexity: O(nlogn)
可借鉴merge sort在递归函数中传递helper arrray的方式将space complexity下降到O(n).


```java
  public TreeNode reconstruct(int[] in, int[] level) {
    Map<Integer, Integer> inorderMap = new HashMap<>();
    for (int i = 0; i < in.length; i++) {
      inorderMap.put(in[i], i);
    }
    List<Integer> levelList = new ArrayList<>();
    for (int elem : level) {
      levelList.add(elem);
    }
    return recur(inorderMap, levelList);
  }

  private TreeNode recur(Map<Integer, Integer> in, List<Integer> level) {
    if (level.isEmpty()) {
      return null;
    }
    TreeNode root = new TreeNode(level.get(0));
    List<Integer> leftLevel = new ArrayList<>();
    List<Integer> rightLevel = new ArrayList<>();
    Iterator<Integer> iter = level.iterator();
    iter.next(); // skip the first element
    // get levelorder list for left and right subtree
    while (iter.hasNext()) {
      int curr = iter.next();
      if (in.get(curr) < in.get(root.key)) {
        leftLevel.add(curr);
      } else {
        rightLevel.add(curr);
      }
    }
    root.left = recur(in, leftLevel);
    root.right = recur(in, rightLevel);
    return root;
  }
```
