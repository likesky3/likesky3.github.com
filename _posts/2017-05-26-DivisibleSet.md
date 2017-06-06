---
layout: post
title:  "DivisibleSet"
date:   2017-05-26 
categories:  DP, Math 
---

Problem Statement  
    	You are given a int[] b containing a sequence of n positive integers: b[0], ..., b[n-1]. We are now looking for another sequence a[0], ..., a[n-1]. This sequence should have the following properties:
Each a[i] should be a number of the form 2^x[i] where x[i] is some positive integer. In other words, each a[i] is one of the numbers 2, 4, 8, 16, ...
For each i, the value a[i]^b[i] (that is, a[i] to the power b[i]) should be divisible by P, where P is the product of all a[i].
Determine whether there is at least one sequence with the desired properties. Return "Possible" (quotes for clarity) if such a sequence exists and "Impossible" otherwise.

Constraints
-	b will contain between 1 and 50 elements, inclusive.
-	Each element in b will be between 1 and 10, inclusive.

由题意可得
a_0^b_0 / prod = k_0, ... a_n-1 ^b_n-1 / prod = k_n-1，其中prod是a_0 * ... * a_n-1的乘积，k_0, k_1, ... k_n均为正整数。
因为a_i = 2^e_i， e_i为正整数，
prod可表示成2^(sum(e_0 + e_1 + ... + e_n-1)),
记S = sum(e_0 + e_1 + ... + e_n-1)，
则e_0 = S * k_0/b_0, e_1 = S * k_1/b_1, ... e_n-1 = S * k_n-1 / b_n-1
将式子左右边各相加，得到S = (k_0/b_0 + k_1/b_1 + ... + k_n-1 / b_n-1) * S，
于是(k_0/b_0 + k_1/b_1 + ... + k_n-1 / b_n-1) = 1，
则题意转化为能否求得k_0, k_1, ... k_n-1使得上述等式成立。
分数计算不方便，我们得想办法化为整数，方法是两边乘以b_0, b_1, ... b_n-1的最小公倍数。
每个k_i至少为1，所以可先给每个k_i分配1，若分配完余量为负数，说明无解，
否则转化为一个背包问题求解余量如何分配。
dp[i][j]表示前面i项 能否装满 j的容量，其实是前面i项能否凑成加和为j。

另外，由于1 <= b_i <= 10, 所以 1/10 <= 1 / b_i <= 1，而每个k_i >= 1, 所以若b[]长度超过10肯定不行, 那样无法满足k_0/b_0 + k_1/b_1 + ... + k_n-1 / b_n-1 = 1.

```java
  public String isPossible(int[] b) {
		if (b.length == 1) {
			return "Possible";
		} else if (b.length > 10) {
			return "Impossible";
		}

		// scm is the smallest common multiple of b[0], b[1] ... b[n - 1]
		int scm = b[0];
		for (int i = 1; i < b.length; i++) {
			scm = scm * b[i] / gcd(scm, b[i]);
		}

		// a_0^b_0 / prod = k_0, ... a_n-1 ^b_n-1 / prod = k_n-1
		// let's a_0 = 2^e_0, ... a_n-1 = 2^e_n-1,
		// then prod = 2^(sum(e_0 + e_1 + ... + e_n-1)),
		// let's S = sum(e_0 + e_1 + ... + e_n-1);
		// so e_0 = S * k_0/b_0, e_1 = S * k_1/b_1, ... e_n-1 = S * k_n-1 / b_n-1
		// then (k_0/b_0 + k_1/b_1 + ... + k_n-1 / b_n-1) * S = S;
		// -->(k_0/b_0 + k_1/b_1 + ... + k_n-1 / b_n-1) * scm = scm; ---(1)
		// the problem become can we find [k_0, k_1, ... k_n-1] so that equation (1) can be satisfied
		int remain = scm;
		for (int i = 0; i < b.length; i++) {
			remain -= scm / b[i];
		}
		if (remain < 0) {
			return "Impossible";
		}
		boolean[][] dp = new boolean[b.length + 1][remain + 1];
		dp[0][0] = true;
		for (int i = 1; i <= b.length; i++) {
			for (int j = 0; j <= remain; j++) {
				dp[i][j] = dp[i - 1][j];
				if (!dp[i][j] && j >= scm / b[i - 1] && dp[i][j - scm / b[i - 1]]); {
					dp[i][j] = true;
				}
			}
		}
		return dp[b.length][remain] ? "Possible" : "Impossible";
	}

	private int gcd(int a, int b) {
		if (a < b) {
			return gcd(b, a);
		}

		int r = 0;
		while (b > 0) {
			r = a % b;
			a = b;
			b = r;
		}
		return a;
	}
```
