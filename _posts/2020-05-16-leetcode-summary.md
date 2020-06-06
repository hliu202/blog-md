---
title: LeetCode 小结
date: 2020-05-16 12:45:32
categories: algorithm
tags: leetcode
---

最近开始做 [LeetCode](https://leetcode-cn.com/)，也参加了 2 次周赛，总结下遇到的问题和套路。

<!--more-->

# 套路

编程语言用的 `Python3`，非常简洁高效，提升积极性。毕竟算法题重在思路，不能被 C/C++和 Java 那冗长的语法分心。

另外用了 `VSCode`写代码和调试，方便灵活，写好后再 copy 到官网页面上提交。

后面就是要多做题了，总结经验，慢慢开始熟练了，跟解数学题一样。

## 想好再写

不要看到题就开始写，除非是做过的或者很简单的题目，否则大概率要卡住，边想边写，写一半又要想，最后还是要完全想通了思路，才能做出来。所以要：

> **理清思路，想好再写**。复杂问题的要打草稿，但也不要没有思路的乱画

在理清思路的过程中，关键是：

- **定义要清晰**。比如 f(i,j) 到底是指范围为：`[i, j]`，还是 `[i, j)`，还是 `(i,  j)` 等，一旦模糊概念，大概率会出错。又比如，结果到底是 `k - i` 还是 `k - (i + 1)` 等
- **活用数据结构**：数组，链表，字典，queue，stack，deque。基本上每道题都会用到
- 想不出来方法，可以看评论和别人的答案

## 相似解法

虽然还没做多少题，已经发现某些题有相似性的**解法的思路**：比如差值法，动态规划，暴力破解（brute force 不可取）等，总结并归纳主题方便后续。

比如：

1. **滑动窗口**：[1425. 带限制的子序列和](https://leetcode-cn.com/problems/constrained-subsequence-sum/) 要用到 [239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)。先做的 1425，看答案也看不懂，就去看的 239 了解什么是`滑动窗口`，搞懂后再回头来搞 1425。
2. **前缀表达式**：[560. 和为 K 的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/) 中的方法，即 `sum(i,j) = sum(0,j) - sum(0,i)`，可以用到 [1442. 形成两个疑惑相等数组](https://leetcode-cn.com/contest/weekly-contest-188/problems/count-triplets-that-can-form-two-arrays-of-equal-xor/)中。第一个题开始是 O(n^2^)的解法，提交结果为`超出时间限制`，看了评论找到解法。后一个题一看和之前遇到的类似，终于靠自己想出了解法
3. **动态规划**（dynamic program)：太多了，基本就是求什么最大xx，最小yy等。重点是 2 个：
   - 明确创建怎样的 **dp 数组**，初始化数组。比如[983. 最低票价](https://leetcode-cn.com/problems/minimum-cost-for-tickets/)，知道要用 dp 思路，但不**按照天**来建立数组，就怎么都想不出解决办法。
   - 明确动态**转移方程**，即 `f(i+1) <- f(i)` 等。比如[221. 最大正方形](https://leetcode-cn.com/problems/maximal-square/)，一开始是 `dp[i][j] = dp[i-1][j-1]+1 if m[i-1][j] == m[i][j-1] == 1`，后来测试出错，发现是要 `min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1])`

## 不要低估问题

每个问题都有它存在的合理性，如果觉得一个问题很简单，很快就写出答案，一般就会：

1. `超出时间限制`：基本上 O(n^2^)的解法都不可取，至少得 O(nlog~n~)，最好是 O(n)
2. `解答错误`：每道题都会给几组测试用例，至少要把它们都跑一遍，没有问题再提交

目前遇到最繁琐的问题是[25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/) （就是今天的每日一题）。题目看上去很简单，翻转链表节点，看上去（确实也）没有未知sao套路，但是难度系数是**困难**？不管，想也不想就开始写代码，结果遇到`解答错误`：

- 少了元素，多了元素，无限循环了。在链表上的操作，其实本身就是难点，要特别注意节点本身以及 next 的关系。

- 不符合题目要求（翻转了最后几个节点）：

  > 如果节点总数不是 *k* 的整数倍，那么请将最后剩余的节点**保持原有顺序**。

## 其他

对于 Python 没有自带的数据结构，自己实现和help functions，方便在本地调试。比如 TreeNode，ListNode 等，还有相应的create, print方法等，待[扩充](https://github.com/hliu202/leetcode-py/tree/master/util)。

# Weekly Contest 周赛

周赛是真正的考验，因为平时的练习题可以看答案，而这个没有答案，且有 1 个半小时限制，又要做出来，还要做的快。一共有 4 道题，简单 x1，中等 x2，困难 x1。

目前参加了 2 场（第二次的题更简单，所以完成时间更短），幸运的是都做出了前 3 道题，最后一道题实在想不出答案，只能找大佬的答案看看。国内和全球竟然是同步的，分别有三千多和一万多人参加，排名如下：

[May-17 189](https://leetcode-cn.com/contest/weekly-contest-189)

| 排名       | 用户名                                                       | 得分 | 完成时间 | [题目1 (3)](https://leetcode-cn.com/contest/weekly-contest-189/problems/number-of-students-doing-homework-at-a-given-time/) | [题目2 (4)](https://leetcode-cn.com/contest/weekly-contest-189/problems/rearrange-words-in-a-sentence/) | [题目3 (5)](https://leetcode-cn.com/contest/weekly-contest-189/problems/people-whose-list-of-favorite-companies-is-not-a-subset-of-another-list/) | [题目4 (7)](https://leetcode-cn.com/contest/weekly-contest-189/problems/maximum-number-of-darts-inside-of-a-circular-dartboard/) |
| ---------- | ------------------------------------------------------------ | ---- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 898 / 3691 | liu-hao-b                                                    | 12   | 1:04:47  | 0:08:52 1                                                    | 0:28:16                                                      | 0:59:47                                                      |                                                              |
| 1          | [tian-tang-6](https://leetcode-cn.com/u/tian-tang-6/)        | 19   | 0:27:31  | 0:01:50                                                      | 0:05:49                                                      | 0:12:10                                                      | 0:27:31                                                      |
| 2          | [zerotrac2](https://leetcode-cn.com/u/zerotrac2/)            | 19   | 0:30:45  | 0:01:44                                                      | 0:04:27                                                      | 0:14:01                                                      | 0:30:45                                                      |
| 3          | [QingchuanZhang-Microsoft](https://leetcode-cn.com/u/qingchuanzhang-microsoft/) | 19   | 0:31:33  | 0:02:24                                                      | 0:05:54                                                      | 0:13:44                                                      | 0:31:33                                                      |

| 排名         | 用户名                                         | 得分 | 完成时间 | [题目1 (3)](https://leetcode-cn.com/contest/weekly-contest-189/problems/number-of-students-doing-homework-at-a-given-time/) | [题目2 (4)](https://leetcode-cn.com/contest/weekly-contest-189/problems/rearrange-words-in-a-sentence/) | [题目3 (5)](https://leetcode-cn.com/contest/weekly-contest-189/problems/people-whose-list-of-favorite-companies-is-not-a-subset-of-another-list/) | [题目4 (7)](https://leetcode-cn.com/contest/weekly-contest-189/problems/maximum-number-of-darts-inside-of-a-circular-dartboard/) |
| ------------ | ---------------------------------------------- | ---- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3670 / 13038 | liu-hao-b                                      | 12   | 1:04:47  | 0:08:52 1                                                    | 0:28:16                                                      | 0:59:47                                                      |                                                              |
| 1            | [kotamanegi](https://leetcode.com/kotamanegi/) | 19   | 0:17:30  | 0:05:48                                                      | 0:04:32                                                      | 0:09:50                                                      | 0:17:30                                                      |
| 2            | [uwi](https://leetcode.com/uwi/)               | 19   | 0:18:45  | 0:18:45                                                      | 0:17:54                                                      | 0:03:28                                                      | 0:13:17                                                      |
| 3            | [x____x](https://leetcode.com/x____x/)         | 19   | 0:20:08  | 0:01:33                                                      | 0:07:22                                                      | 0:13:58                                                      | 0:20:08                                                      |

[May-24 190](https://leetcode-cn.com/contest/weekly-contest-190)

| 排名       | 用户名                                                       | 得分 | 完成时间 | [题目1 (3)](https://leetcode-cn.com/contest/weekly-contest-190/problems/check-if-a-word-occurs-as-a-prefix-of-any-word-in-a-sentence/) | [题目2 (4)](https://leetcode-cn.com/contest/weekly-contest-190/problems/maximum-number-of-vowels-in-a-substring-of-given-length/) | [题目3 (5)](https://leetcode-cn.com/contest/weekly-contest-190/problems/pseudo-palindromic-paths-in-a-binary-tree/) | [题目4 (6)](https://leetcode-cn.com/contest/weekly-contest-190/problems/max-dot-product-of-two-subsequences/) |
| ---------- | ------------------------------------------------------------ | ---- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 649 / 3350 | liu-hao-b                                                    | 12   | 0:32:29  | 0:05:11                                                      | 0:14:23 1                                                    | 0:27:29                                                      |                                                              |
| 1          | [liouzhou_101](https://leetcode-cn.com/u/liouzhou_101/)      | 18   | 0:11:50  | 0:02:34                                                      | 0:04:56                                                      | 0:08:47                                                      | 0:11:50                                                      |
| 2          | [Heltion](https://leetcode-cn.com/u/heltion/)                | 18   | 0:14:32  | 0:03:59                                                      | 0:06:39                                                      | 0:09:36                                                      | 0:14:32                                                      |
| 3          | [QingchuanZhang-Microsoft](https://leetcode-cn.com/u/qingchuanzhang-microsoft/) | 18   | 0:14:34  | 0:01:52                                                      | 0:03:58                                                      | 0:08:59                                                      | 0:14:34                                                      |

| 排名         | 用户名                                             | 得分 | 完成时间 | [题目1 (3)](https://leetcode-cn.com/contest/weekly-contest-190/problems/check-if-a-word-occurs-as-a-prefix-of-any-word-in-a-sentence/) | [题目2 (4)](https://leetcode-cn.com/contest/weekly-contest-190/problems/maximum-number-of-vowels-in-a-substring-of-given-length/) | [题目3 (5)](https://leetcode-cn.com/contest/weekly-contest-190/problems/pseudo-palindromic-paths-in-a-binary-tree/) | [题目4 (6)](https://leetcode-cn.com/contest/weekly-contest-190/problems/max-dot-product-of-two-subsequences/) |
| ------------ | -------------------------------------------------- | ---- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3020 / 11873 | liu-hao-b                                          | 12   | 0:32:29  | 0:05:11                                                      | 0:14:23 1                                                    | 0:27:29                                                      |                                                              |
| 1            | [huzecong](https://leetcode.com/huzecong/)         | 18   | 0:08:02  | 0:01:08                                                      | 0:02:24                                                      | 0:04:41                                                      | 0:08:02                                                      |
| 2            | [zcgzcgzcg](https://leetcode.com/zcgzcgzcg/)       | 18   | 0:09:06  | 0:01:20                                                      | 0:03:01                                                      | 0:06:24                                                      | 0:09:06                                                      |
| 3            | [superluminal](https://leetcode.com/superluminal/) | 18   | 0:09:54  | 0:01:06                                                      | 0:02:14                                                      | 0:04:39                                                      | 0:09:54                                                      |

# 总结

以前有在英文 LeetCode 网站上也做过十几道题，没有坚持，也没总结经验。

现在重现开始做做题，起因是 B 站上看到有大神发周赛的视频，看了之后觉得

> 卧槽，你为什么这么熟练？

觉得身为程序员，也要做基本的练习，避免退步。今年目标是 365 题，[我的主页](https://leetcode-cn.com/u/liu-hao-b/)，进度条还在初级阶段...
