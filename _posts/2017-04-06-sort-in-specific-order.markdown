---
layout: post
title:  "Sort In Specific Order"
date:   2017-04-06 
categories:  sort 
---

**Description**  
Given two integer array A1 and A2, sort A1 in the way that the relative order among elements will be same as those in the A2. For elements not appeared in A2, append them to the right end of A1 in ascending order.  
Assumption  
1. A1 and A2 are not null.  
2. There are no duplicate elements in A2.  

**Solution**  
JDK 提供有sort array的API，但这里不能直接使用，因为A1元素间的排序不仅仅是看元素值的相对大小。为解决问题，我们需要重写符合需求的comparator，comparator的作用是告诉sort函数如何判定两个元素a, b的相对大小。具体到本题，可分如下三种情况讨论：  
case 1: a和b均属于A2，则a和b的相对大小看它们在A2中的相对位置，靠前的元素判为“更小”。  
case 2: a和b有一个属于A2, 则将属于A2的那个元素排在前面。  
case 3: a和b均不属于A2，则根据它们的值大小判断，值小排前。  
为方便获取一个元素是否存在A2中，若存在，下标是多少这些信息，我们可将A2的value和index作为key-value存放在一个HashMap中。  
这个算法的Time complexity为：O(nlogn + m), n=A1.length, m=A2.length.代码如下：
PS: 之所以需要将A1转为Integer[]进行sort，是因为API只提供对Object类型的数组自定义comparator.  


```Java
private static class MyComparator implements Comparator<Integer> {
    private Map<Integer, Integer> map;
    public MyComparator(int[] A2) {
      map = new HashMap<>();
      for (int i = 0; i < A2.length; i++) {
        map.put(A2[i], i);
      }
    }
    
    @Override
    public int compare(Integer a, Integer b) {
      Integer aIdx = map.get(a);
      Integer bIdx = map.get(b);
      if (aIdx != null && bIdx != null) {
        return aIdx.compareTo(bIdx);
      } else if (aIdx == null && bIdx == null) {
        return a.compareTo(b);
      }
      return aIdx != null ? -1 : 1;
    }
  }
  
  public int[] sortSpecial(int[] A1, int[] A2) {
    if (A1.length == 0) {
      return A1;
    }
    Integer[] helper = toIntegerArray(A1);
    Arrays.sort(helper, new MyComparator(A2));
    return toIntArray(helper);
  }
  
  private Integer[] toIntegerArray(int[] A1) {
    Integer[] helper = new Integer[A1.length];
    for (int i = 0; i < A1.length; i++) {
      helper[i] = Integer.valueOf(A1[i]);
    }
    return helper;
  }
  
  private int[] toIntArray(Integer[] helper) {
    int[] ret = new int[helper.length];
    for (int i = 0; i < helper.length; i++) {
      ret[i] = helper[i];
    }
    return ret;
  }
 ```
