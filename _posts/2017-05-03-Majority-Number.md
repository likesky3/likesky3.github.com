---
layout: post
title:  "Majority Number Problem"
date:   2017-05-03 
categories:   
---

**Problem:**  
Given an array of size n, find the majority element. The majority element is the element that appears more than ⌊ n/2 ⌋ times.

You may assume that the array is non-empty and the majority element always exist in the array.

Follow up 1: What about more than  ⌊ n/3 ⌋ ?
Follow up 2: What about more than  ⌊ n/k ⌋ , k > 2?

**Solution:**  
解题过程可以形象化为土匪火拼过程。  
⌊ n/2 ⌋: one v.s one,  
⌊ n/3 ⌋, one v.s. two,  
⌊ n/k ⌋, one v.s. k - 1  

When a new element x comes in, we compare it with currently remained elements (winner candidates):  
Case 1:  number of currently remained elements < k - 1, just insert into the hashmap or update its counter if existed already.  

Case 2: number of candidates >= k - 1  
- Case 2.1: if x equals none, all the k - 1 elements decrease by 1, if one decreases to 0, remove it from hashmap. // 每个团伙损兵一枚，玉石俱焚  
- Case 2.2:  if x equals with one of the candidates, the candidates increase 1. // all survivored

**Proof by induction**:  
Initially, A > 1/k * total.  
Suppose after m round, A still > 1/k * remained total, #A= n + 1, #remained=n * k + r;  

Let's look t the m+1 th round:  
case 1: the challenger is A
- case 1.1: if A is among the k - 1 winners, situation remains same
- case 1.2: if A is not among the k - 1 winners, then current A dead and all winners will also lose one member, #A / # remained = n / ((n-1) * k + r), still > 1 / k

case 2: the challenger is not A say it’s B
- case 2.1: if B is among the k - 1 winners, situation remains same
- case 2.2: if B is not amont the k - 1 winners, we would remove k elements  from total, if A is among the k elements, similar to case 1.2, if A is not, then #A remains, #remained decrease, so > 1/k
so we prove that after the whole process, A would always > 1/k, so A must be in the final remains


```Java
class Solution {
	// Total time complexity is O(n), space complexity is O(k)
	public List<Integer> majority(int[] array, int k) {
		// key is some array[i], value is current remained number of array[i]
		Map<Integer, Integer> candidates = new HashMap<>();

		// O(n), each element in array[] go in and go out to the hashmap at most
		// once
		for (int i = 0; i < array.length; i++) {
			Integer myVotes = candidates.get(array[i]);
			if (myVotes == null) { // comes a new candidate
				if (candidates.size() < k - 1) { // if there is empty position, just comes in
					candidates.put(array[i], 1);
					continue;
				}
				List<Integer> toRemove = new ArrayList<>();
				for (Map.Entry<Integer, Integer> cand : candidates.entrySet()) {
					if (cand.getValue() == 1) {
						toRemove.add(cand.getKey());
					} else {
						cand.setValue(cand.getValue() - 1);
					}
				}
				for (Integer loser : toRemove) {
					candidates.remove(loser);
				}
			} else {
				candidates.put(array[i], myVotes + 1);
			}

		}
		
		// reset the counter of each candidate to 0, O(k)
		for (Integer cand : candidates.keySet()) {
			candidates.put(cand, 0);
		}

		// scan the input again to count the actual number of each candidate,
		// O(n)
		for (int cand : array) {
			Integer myVotes = candidates.get(cand);
			if (myVotes != null) {
				candidates.put(cand, myVotes + 1);
			}
		}

		// select those > 1 / k, O(k)
		List<Integer> result = new ArrayList<>();
		for (Integer cand : candidates.keySet()) {
			if (candidates.get(cand) > array.length * 1.0 / k) {
				result.add(cand);
			}
		}
		return result;
	}
}
```

