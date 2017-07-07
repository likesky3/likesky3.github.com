---
layout: post
title:  "Egg Dropping Puzzle"
date:   2017-07-06 
categories:  DP 
---

Problem:  
Suppose that we wish to know which stories in a 100-story building are safe to drop eggs from, and which will cause the eggs to break on landing. What strategy should be used to drop eggs such that total number of drops in worst case is minimized and we find the required floor.

We may make a few assumptions:

An egg that survives a fall can be used again.
A broken egg must be discarded.
The effect of a fall is the same for all eggs.
If an egg breaks when dropped, then it would break if dropped from a higher floor.
If an egg survives a fall then it would survive a shorter fall.

Solution Ref:  
http://datagenetics.com/blog/july22012/index.html  
http://www.geeksforgeeks.org/puzzle-set-35-2-eggs-and-100-floors/  
http://www.geeksforgeeks.org/dynamic-programming-set-11-egg-dropping-puzzle/  

```Java
   // 2 egg problem
	public int minDrops(int n) {
		int[] dp = new int[n + 1];
		for (int i = 1; i <= n; i++) {
			dp[i] = i;
			for (int j = 1; j <= i; j++) {
				dp[i] = Math.min(dp[i], 1 + Math.max(j - 1, dp[i - j]));
			}
		}
		return dp[n];
	}
	
	// k eggs problem
	public int minDrops(int floors, int eggs) {
		int[][] dp = new int[floors + 1][eggs + 1];
		Arrays.fill(dp[1], 1);
		for (int i = 2; i <= floors; i++) {
			dp[i][1] = i;
			for (int j = 2; j <= eggs; j++) {
				dp[i][j] = i;
				for (int k = 1; k <= i; k++) {
					dp[i][j] = Math.min(dp[i][j], 1 + Math.max(dp[k - 1][j - 1], dp[i - k][j]));
				}
			}
		}
		return dp[floors][eggs];
	}
```

