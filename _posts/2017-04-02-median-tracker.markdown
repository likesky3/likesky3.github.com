---
layout: post
title:  "Median Tracker"
date:   2017-04-03 
categories:  PriorityQueue 
---

**Description**  
Given an unlimited flow of numbers, keep track of the median of these numbers.
You will have to implement 2 methods:
* read(int value) - read one value from the flow
* median() - return the median at any time, return null if there is no value so far

[leetcode link](https://leetcode.com/problems/find-median-from-data-stream/#/description)

**Solution**  
 首先naive的解法是什么？  
 naive版本1: sort已经读到的所有数据，按照median定义找到median。Time complexity: O(nlogn)  
 naive版本2：维护已读数据为有序状态，新数据进来插入正确位置，然后返回median。Time complexity: O(n)  
 optimal版本: median的定义是数组中比median大和比median小的元素个数相同，如果somehow我们能将数组分为两半(至多相差1)，smaller & larger，并且维护smaller中的最大值以及larger中的最小值,则median可通过这两个边界极值算得。这两个极值正是数组的中间值，若数组size为奇数，则为两个中index为奇数的那个，若数组size为偶数，则是两个值加和取平均。那如何维护smaller half和larger half以及它们的边界极值呢？我们只关心每一部分的最大值或者最小值，并不care其余元素的相对大小，PriorityQueue这个数据结构非常适合处理这种情况。所以我们用一个maxHeap来存储smallerHalf, 用一个minHeap来存储largerHalf。在处理数据流过程中我们始终保持smallerHalf.size不比largerHalf.size小但至多不超过1，以此维持两半差不多。  
 具体实现分两种情况讨论：  
 - case1 : smallerHalf.size == largerHalf.size, push new element into smallerHalf
 - case2 : smallerHalf.size == largerHalf.size + 1，
   - case 2.1: if new element is larger than max of smallerHalf, add it to largerHalf 
   - case 2.2: otherwise, remove max of smallerHalf to largerHalf, then add the new element to smallerHalf  
  代码中largerHalf.size >= smaller.size  

```Java
class Solution {
	// store the smaller 50%
	private PriorityQueue<Integer> smallerHalf;
	// store the larger 50%
	private PriorityQueue<Integer> largerHalf;

	public Solution() {
		smallerHalf = new PriorityQueue<>(Collections.reverseOrder());
		largerHalf = new PriorityQueue<>();
	}

	// largerHalf.size() >= smallerHalf.size()
	public void read(int value) {
		if (largerHalf.size() == smallerHalf.size()) {
			largerHalf.offer(value);
		} else {
			if (value < largerHalf.peek()) {
				smallerHalf.offer(value);
			} else {
				smallerHalf.offer(largerHalf.poll());
				largerHalf.offer(value);
			}
		}
	}

	public Double median() {
		if (largerHalf.isEmpty()) {
			return null;
		} else if (largerHalf.size() > smallerHalf.size()) {
			return Double.valueOf(largerHalf.peek());
		} else {
			return (largerHalf.peek() + smallerHalf.peek()) / 2.0;
		}
	}
}
```
