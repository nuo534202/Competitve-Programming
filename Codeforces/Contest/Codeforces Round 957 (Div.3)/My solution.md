# My solution
Contest Link: [Codeforces Round 957](https://codeforces.com/contest/1992)

My Blog Link: [CF957](https://blog.csdn.net/djhws144/article/details/140376880?spm=1001.2014.3001.5501)

## A. Only Pluses
每次都选取最小的数进行操作，最后就会得到最大的结果。

## B. Angry Monk
对于所有 $a_i = 1$ 的块，可以直接与最大的块合并，每次合并需要进行 $1$ 次操作。

对于剩下的 $a_i \ge 2$ 的块，除了最大的块以外，每一个块可以先分成 $a_i$ 个长度为 $1$ 块，再分别与最大的块进行合并，分别需要进行 $a_i - 1$ 和 $a_i$ 次操作。

很容易发现，以上两种情况都需要进行 $2a_i - 1$ 次操作。

## C. Gorilla and Permutation
要让结果最大，我们很容易能想到要让 $\sum_{i = 1}^{n}f(i)$ 尽可能大，并且要让 $\sum_{i = 1}^{n}g(i)$ 尽可能小，
所以只需要将所有不小于 $k$ 的数由大到小地放在排列的最前面，将所有不大于 $m$ 地数有小到大地放在排列的最后面；中间的数字可以以任何数字排列，对结果不会产生任何影响。

## D. Test of Love
从左到右的最少游泳距离一定是等于从右到左的最少游泳距离的，因此可以反向考虑，从最右端跳到最左端需要最少的游泳距离是多少。设 $f_i$ 为从后往前考虑到第 $i$ 位最少的游泳距离。

## E. Novice's Mistake
从 $1$ 到 $10000$ 枚举 $b$，再把 $n$ 当作字符串按位枚举 $x$，根据 $x$ 推出对应的 $a$；判断 $x$ 是否等于 $n$ 位数字时的 。

如何得到 $a$：把 $x$ 看作字符串的时候，$x$ 是由 $a$ 个 $n$（字符串）再减去最后 $b$ 个字符得到。因此，只有 $x$（字符串）的长度 + $b$ 为 $n$（字符串）的长度的倍数时，$a$ 才存在。

## F. Valuable Cards
要使坏的片段最少，那么要使每个坏的片段尽可能的长。从最左边的卡片开始遍历，用 $set$ 或者 $map$ 来存能凑出乘积为 $x$ 的子集的因数。
- 如果 $set$ 或者 $map$ 中已经存有 ，那么 $ans = ans + 1$，并且清空 $set$ 或者 $map$
- 否则，遍历 $set$ 或者 $map$，将 $set$ 或者 $map$ 的第一个元素除以 $a_i$ 的结果分别放入 $set$ 或者 $map$ 中；再将  放入 $set$ 或者 $map$ 中

（比赛时用的 unordered_map 没过.....，有没有人知道为什么用 unordered_map 过不了）
