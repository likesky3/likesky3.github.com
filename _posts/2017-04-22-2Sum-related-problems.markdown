---
layout: post
title:  ""
date:   2017-- 
categories:  xx 
---

[2 Sum Q1]: Given an integer array nums[] and an integer target, find if there exist a pair integers whose sum equals to target.
[2 Sum Q2]: Given an integer array nums[] and an integer target, find all distinct pairs integers whose sum equals to target.
[2 Sum Q3]: Given an integer array nums[] and an integer target, find the indices  of all pairs whose sum equals to target. (in returned pair indices, smaller index comes first)

Before diving into solving the problem, we need to ask and clarity the following information:
1) Is the input sorted or not?
2) What to optimize? time or space


There are there regular methods:
0) enumerate all pairs and check, time: O(n^2)
1) Sort the array and use Two Pointers trick.
Time: O(nlogn + n), Space: O(n), used by sorting.
If use selection sort, time go up to O(n^2), but space go down to O(1)
2) Use a hashtable to record what we have scanned.
Time: O(1), Space: O(n^2)

How about **3 Sum**?
Method 1: enumerate the first element, then do a 2 Sum in the left elements.
Method 2: First, enumerate all pairs, put them into a HashMap, key is the pair sum, value is indices of the two number. Second, for loop each element, check hashmap to see if there exists a pair whose sum is target - current element.  Time: O(n^2), Space: O(n^2)
_**Method 2 is more general and clear in logic**_.

How about **4 Sum**?
First, generate all two sum pairs into a hashmap. Second, enumerate the first 2 elements and check hashmap to see if we can find the rest 2 elements.Time: O(n^2), Space: O(n^2)

How about **5 Sum**?
First, preprocess,genereate all two sum pairs into a hashmap. Second enumerate the first 3 elements and check. Time: O(n^3), Space: O(n^2)

How about **6 Sum**?
First, preprocess, generate all tuples into a hashmap. Second enumerate the first 3 elements and check. Time: O(n^3), Space: O(n^3)

How about **k Sum**?
Using method of the above, for k Sum, we can build the result in 2 steps:
First, preprocess, generate all A-size vector into a hashmap.
Second, enumerate the k - A elements and check hashmap to see if there exists a corresponding A vector, so that sum of the (k - A) + A elements equals to target.
Time: O(n^((k + 1) / 2), Space: O(n^(k / 2))

If k-sum of the input array is relativly small, we can use dynamic programming method to solve the problem in a manner similar like partition problem. dp[i][s][k] means there exists a k-size vector whose sum is s in the first i elements of the input array. The time complexity of dp is O(n * targetSum * k).

In order to deduplicate, the easiest way is using a HashSet. As long as it does not increase the total time and space complexity, use it. The other way is to sort the array first and then building the result in a ordered manner. The latter seems clever but must be very careful when implement it and it's get much more complicated when k become large.

