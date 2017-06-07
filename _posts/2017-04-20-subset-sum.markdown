---
layout: post
title:  "Subset Sum"
date:   2017-04-20 
categories:  DP 
---

Subset Sum问题是说给定一个集合，能否找到一个subset其元素加和为0，这是一个NP-Complete的问题，不过如果问题规模比较小是可解的，这里的问题规模有两个特征来衡量，特征一是set的总大小N，特征二是set所有元素加和S, 任何一个特征比较小就可解。当N比较小时，可枚举全部subset，逐个check，时间复杂度是O(2^N)。当S比较小时，可使用dynamic programming(DP)来求解， 时间复杂度是O(sum * N)。  

Subset sum的一个经典变种问题时partition problem,即给定一个set，判断能否将其分为两个subset, 它们各自元素的加和相等。另一个类似变种是求解如何切分set使得切分得到的两个subset的subset sum最接近。实际解决问题时有很多细节需要仔细考虑和讨论，或者说需要明确问题的前提假设或者说user case:  
1. input set的size最大可能是多少？
2. input set的sum最大可能是多少？
3. input都是非负整数还是正负数都有？

此外可能有的Follow up问题：1）输出所有可能解，2）partition成 k份sum 相等的subset如何处理

枚举所有subset依次check适宜解决N <= 20的情况。DP解法实际上是背包问题的一个应用，dp[i][j]表示前面i个元素中包含有subset sum为 j的subset, 需要注意的是当input包含负数时，dp[i][j]的两个维度先后顺序不能改变，考察的元素个数是单调递增的，但是sum在有负数情况下并不是单调的，会忽大忽小。

partition成 k 个equal sum的subset时，需要先对k 分类讨论，然后递归地去寻找k个符合条件的subset。

DP方法求解partition two equal sum subsets代码：
```java
public boolean partitionEqualSum(int[] nums){
	// find min & max sum
	int minSum = 0, maxSum = 0;
	int totalSum = 0;
	for (int elem : nums) {
		if (elem <= 0) {
			minSum += elem;
		} else {
			maxSum += elem;
		}
		totalSum += elem;
	}
	if (Math.abs(totalSum) % 2 == 1) { // >< better than totalSum % 2, since -3 % 2 == -2, which wold lead wrong result
		return false;
	}
	int offset = minSum < 0 ? -minSum : 0;
	int n = nums.length;
	int halfSum = totalSum / 2;
	boolean[][] dp = new boolean[n + 1][maxSum - minSum + 1]; // >< not [halfSum + offset + 1][n + 1], not[halfSum - minSum + 1][n + 1]
	dp[0][0 + offset] = true;
	for (int i = 1; i <= n; i++) {
		for (int j = minSum + offset; j <= maxSum + offset; j++) { // >< when there are negative elements in the input, upper limit need to be maxSum + offset
			dp[i][j] = dp[i - 1][j];
			if (!dp[i][j] && j - nums[i - 1] >= 0 && j - nums[i - 1] < dp[0].length) { // >< do not forget: i - nums[j - 1] < dp.length
				dp[i][j] = dp[i - 1][j - nums[i - 1]]; // // >< since nums[j - 1] may be a negative number, so i - nums[j - 1] may be greater than i
			}
		}
	}
	
	return dp[n][halfSum + offset];
}
```

递归解决分为三份equal sum代码：
实现中使用HashSet时为了给结果去重，仅使用HashSet不足以实现这个目的，需要在构造结果时保证特定的顺序，实现中是对各个subset进行sort。
```java
public static void divide(int[] nums) {
	int totalSum = 0;
	for (int n : nums) {
		totalSum += n;
	}
	
	if (totalSum % 3 == 0) {
		Set<List<List<Integer>>> result = new HashSet<>();
		boolean[] visited = new boolean[nums.length];
		recur(nums, visited, 0, result, new ArrayList<>(), new ArrayList<Integer>(), 0, totalSum / 3);
		for (List<List<Integer>> solu : result) {
			print(solu);
		}
	}
}

private static void recur(int[] nums, boolean[] visited, int start, Set<List<List<Integer>>> result, List<List<Integer>>  solu, List<Integer> part, int partSum, int sum) {
	if (partSum == sum) {
		List<Integer> copy = new ArrayList<Integer>(part);
		Collections.sort(copy);
		solu.add(copy);
		if (solu.size() == 1) {
			recur(nums, visited, 0, result, solu, new ArrayList<>(), 0, sum); // search for 2nd part
		} else { // solu.size == 2
			List<Integer> part3 = new ArrayList<>();
			for (int i = 0; i < nums.length; i++) {
				if (!visited[i]) {
					part3.add(nums[i]);
				}
			}
			Collections.sort(part3);
			solu.add(part3);
			List<List<Integer>> soluCopy  = new ArrayList<>(solu);
			Collections.sort(soluCopy, new PartitionComp());
			result.add(soluCopy);
			solu.remove(solu.size() - 1); // >< backtrace, restore status
		} 
		solu.remove(solu.size() - 1); // >< backtrace, restore status
		return;
	} /* else if (partSum > sum) { // >< partSum may decrease later
		return;
	} */ 
	
	while (start < nums.length && visited[start]) {
		start++;
	}
	if (start == nums.length) {
		return;
	}
	
	// choose nums[start]
	part.add(nums[start]);
	visited[start] = true;
	recur(nums, visited, start, result, solu, part, partSum + nums[start], sum);
	visited[start] = false;
	part.remove(part.size() - 1);
	
	// do not choose nums[start]
	recur(nums, visited, start + 1, result, solu, part, partSum, sum);
}
	
```





