---
layout: post
title:  "Ugly Numbers"
date:   2017-04-26
categories:  Best-First-Search 
---


[Super Ugly Number](https://leetcode.com/problems/super-ugly-number/#/description): Write a program to find the nth super ugly number.

Super ugly numbers are positive numbers whose all prime factors are in the given prime list primes of size k. For example, [1, 2, 4, 7, 8, 13, 14, 16, 19, 26, 28, 32] is the sequence of the first 12 super ugly numbers given primes = [2, 7, 13, 19] of size 4.
Note:
(1) 1 is a super ugly number for any given primes.
(2) The given numbers in primes are in ascending order.
(3) 0 < k ≤ 100, 0 < n ≤ 106, 0 < primes[i] < 1000.
(4) The nth super ugly number is guaranteed to fit in a 32-bit signed integer.

Signature: int nthUglyNum(int n, int[] primes)

**Solutions**
以下讲解中以三个质数因子{2, 3, 5}为例.
[Method 1] Basic Best First Search

我们使用一个minHeap存储所有候选的数字，每个数字形式化表示是f(x, y, z)=2^x * 3 ^y * 5 ^z。 每次循环获取当前堆顶，假设堆顶数字是f(x, y, z)，则这个数再乘以一个质数，可以得到f(x + 1, y, z)或者f(x, y+1, z), 或者f(x, y, z + 1),把这三个数插入堆中。如此循环 n 次便可得到结果。

注意到每个f(x, y, z) 可由f(x - 1, y, z)或者f(x, y - 1, z)或者f(x, y, z - 1) 生出，所以需要有去重措施。去重有两种方式，方式1：使用一个hashset，若新生成元素已经在hashset中则不再插入heap, 否则插入heap. 方式2：每次从heap中获取堆顶后查看堆顶元素是否和刚弹出的相同，若相同则继续弹出。

下面分析该算法的时间空间复杂度。如果有 k 个质数，则每次循环往heap中插入k个新数，循环n次，（不考虑去重）共插入nk个数，因此整个过程有n次minHeap.poll, nk次minHeap.offer, 单次poll或者offer的时间复杂度均是lognk, 实际上因为要去重, 会有更多次poll和offer，额外的次数就是生成 n 个uglyNum过程中产生的重复数。对于某个数，最后一个因子最坏情况下可能是k个primes中的任意一个，也就是说考虑最后一个因子是谁则一个数可能有 k 种方式获得，所以n个数 最坏可能产生出nk个重复值。因此使用hashset去重则整个过程最多可能有 n次poll和nk次offer，还有nk + nk次尝试加入hashset, 所以总的时间复杂度是nklognk， 空间复杂度是O(nk)。
```java
public int nthSuperUglyNumber(int n, int[] primes) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        minHeap.offer(1);
        Set<Integer> visited = new HashSet<>();
        for (int i = 1; i < n; i++) {
            int curr = minHeap.poll(); // O(nlognk)
            for (int j = 0; j < primes.length; j++) {
                if (curr > Integer.MAX_VALUE / primes[j]) {
                    break;
                }
                int cand = (long)curr * primes[j];
                if (visited.add((int)cand)) {// O(nk)
                    minHeap.offer((int)cand); // O(nklognk)
                }
            }
        }
        return minHeap.peek();
    }
```
[Method 2] Optimized Best First Search
Method 1是应用BFSII的基础思路，expand一个元素，generater 等待后续处理的新元素。因为input个的 k 个primes是不同的，我们可以让其按照升序排列。假设当前堆顶元素是curr=prevVal * primes[i], 则curr出堆后我们仅需要插入prevVal * primes[i] * primes[i] (新元素规则1)和prevVal * primes[i + 1] （新元素规则2），这两个元素显然比curr大。这样做的好处是省的去重了。因为每次新插入heap的都是更大的数，不会重复。为什么这个算法是正确的？需要证明两件事，一是按照这个规则能够枚举出所有的uglysNum， 二是最小的数一定在minHeap中。

首先使用数学归纳法证明观点1。1和primes[0]都是初始化会枚举的，现在要证明
X = primes[0]^x[0] * primes[1]^x[1] * ... * primes[k - 1] * x[k - 1],
记为 X = f(x[0], x[1],... x[k - 1])
能够被枚举到，假设任意比它小的数能够枚举得到，也就是说
Y = primes[0]^y[0] * primes[1]^y[1] * ... * primes[k - 1] * y[k - 1],
记为 Y = f(y[0], y[1],... y[k - 1])
已经被枚举(对于任意i满足y[i] <= x[i]， 并且存在j使得y[j] < x[j]).

假设第 i 项primes[i] * x[i]是最后一个 x[i] > 0的项，若x[i] == 1, 则 X = f(x[0], x[1], ... x[i - 1], **0**, ... x[k - 1]) * primes[i] （新元素规则2）， 若x[i] > 1, 则X = f(x[0], x[1], ... x[i - 1], **x[i] - 1**, ... x[k - 1]) * primes[i] (新元素规则1)。 得证。

接着使用反证法证明观点2。所有数分为已输出的最小序列，在minHeap中的序列以及未枚举到的序列。假设接下来最小的数在未枚举的序列中，则比它小的数已输出的最后一个，按照我们的规则，已输出的数会将可由它构造且比它大的最小两个数插入minHeap中，矛盾。

类似此处在推进规则时增加约束条件(只能一个方向前进，这里加入heap的数若写成p[0] * p[1] * .. p[m], 则对于任意的i < j, p[i] <= p[j]， 以此确保不会有重复，因为新加入的元素要么比之前的数多一项要么最后一个因子增大了)，这种技巧在很多枚举问题的去重上都有效，比如枚举一个数的所有因式。

```java
static class Node {
        int prevVal;
        int lastPrimeIdx;
        public Node (int prevVal, int idx){
            this.prevVal = prevVal;
            lastPrimeIdx = idx;
        }
    }
    static class NodeComparator implements Comparator<Node> {
        int[] primes;
        public NodeComparator(int[] primes) {
            this.primes = primes;
        }
        @Override
        public int compare(Node a, Node b) {
            long aVal = (long)a.prevVal * primes[a.lastPrimeIdx];
            long bVal = (long)b.prevVal * primes[b.lastPrimeIdx];
            if (aVal == bVal) {
                return 0;
            }
            return aVal < bVal ? -1 : 1;
        }
    }
    public int nthSuperUglyNumber(int n, int[] primes) {
       if (n == 1) {
           return 1;
       }

       int k = primes.length;
       PriorityQueue<Node> minHeap = new PriorityQueue<>(n, new NodeComparator(primes));
       minHeap.offer(new Node(1, 0));
       for (int i = 1; i < n - 1; i++) {
           Node curr = minHeap.poll();
           if (curr.lastPrimeIdx + 1 < k) {
               minHeap.offer(new Node(curr.prevVal, curr.lastPrimeIdx + 1));
           }
           if ((long)curr.prevVal * primes[curr.lastPrimeIdx] <= Integer.MAX_VALUE) {
            minHeap.offer(new Node(curr.prevVal * primes[curr.lastPrimeIdx], curr.lastPrimeIdx));
           }
       }
       Node nthNode = minHeap.poll();
       return nthNode.prevVal * primes[nthNode.lastPrimeIdx];
    }

```



