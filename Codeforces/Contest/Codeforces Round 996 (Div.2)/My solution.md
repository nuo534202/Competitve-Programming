# My solution

Contest Link: [Codeforces Round 996](https://codeforces.com/contest/2055) 

My Blog Link: [CF996](https://blog.csdn.net/djhws144/article/details/145137970?sharetype=blogdetail&sharerId=145137970&sharerefer=PC&sharesource=djhws144&spm=1011.2480.3001.8118)

# A. Two Frogs
答案只与 $Alice$ 和 $Bob$ 之间距离的奇偶有关。

- 当 $Alice$ 和 $Bob$ 之间的距离为奇数的时候， $Alice$ 只要一直朝着 $Bob$ 的方向走，最后 $Alice$ 一定获胜。
- 当 $Alice$ 和 $Bob$ 之间的距离为偶数的时候， $Bob$ 只要一直朝着 $Alice$ 的方向走，最后 $Bob$ 一定获胜。

时间复杂度： $O(t)$。

# B. Crafting
- 当存在两个 $1 \le i \le n$，使得 $a_i < b_i$，那么不可能成功制作工艺品，因为当其中一个 $a_i$ 增大的时候，另一个 $a_j$ 会相应减小且他们的总和不变，即 $a_i + a_j$ 为定值，又 $a_i + a_j < b_i + b_j$，所以不可能成功。
- 如果初始状态下的 $1 \le i \le n$ 都满足 $a_i \ge b_i$，那么一定成功。
- 如果存在唯 一 一 个 $i$，使得 $a_i < b_i$，只需要检查对于所有的 $1 \le j \le n$, $j \not= i$，是否满足 $a_j - b_j \ge b_i - a_i$ 即可。

时间复杂度： $O(\sum_{i = 1}^{t}n)$。

# C. The Trail
假设最初的矩形网络每一行每一列的和都为 $x$，那么 $nx = mx$，即 $(n - m)x = 0$

- 当 $n \not= m$ 即 $n - m \not= 0$时， $x$ 一定为 $0$。 
- 当 $n = m$ 即 $n - m = 0$ 时， $x$ 可以为任意实数。

实现细节：当前位置对应的移动方向为 D DD 时，优先满足行，否则优先满足列。

时间复杂度： $O(\sum_{i = 1}^{t}mn)$。

# D. Scarecrow
1. 可以观察到第一步是让位于 $a_1$ 的稻草人移动到位置 $0$，然后青蛙跳到位置 $k$。
2. 并且为了让时间最优，稻草人的位置一定不能交叉。
3. 能利用下一个稻草人往前跳一定要跳：如果不能跳，那么就要让上一个位置的稻草人往右移，下一个稻草人往左移，直到青蛙能跳到下一个稻草人所在的位置为止，这个过程相当于上一个稻草人在 “推” 着青蛙往前走，对于这个 “推” 的操作，往前推的距离是等于时间的，而且每个稻草人都一样，所以能跳一定要往前跳。

记当前位置为 $pos$，花费的时间为 $ans$，每一次移动完之后可能会出现以下情况，

- $pos \ge a_i - ans$，也就是说当前花费的时间 **足够** 下一个稻草人移到当前青蛙跳到的位置，那么青蛙可以直接跳到 $pos + k$ 的位置。
- $pos < a_i - ans$，也就是说说当前花费的时间 **不够** 下一个稻草人移到当前青蛙跳到的位置，那么需要下一个位置的稻草人，跟上一个位置的稻草人同时向中间移动 $\frac{a_i - ans - pos}{2}$ 的时间，然后青蛙正好就能跳到移动后下一个 $a_i$ 的位置然后再跳到 $a_i + k$。

跳到当 $pos \ge l$ 的时候直接输出答案。

时间复杂度： $O(\sum_{i = 1}^{t}n)$。

## E. Haystacks
1. 首先我们假定一个操作顺序 $p_{1, 2, \cdots, n}$。
2. 我们可以发现每一个干草最多被操作两次。
	- 如果前面的干草堆还没有达到最大值，移动到前面的干草堆，总共操作 $1$ 次。
	- 否则，先移动到最后一个干草堆，最后再在清空最后一个干草堆时前移，总共操作 $2$ 次。
3. 先清空所有 $a_i \le b_i$ 的干草堆，再清空 $a_i > b_i$ 显然是最优的。当 $(a_x \le b_x) = (a_y \le b_y)$ 时，如果位于 $y$, $x$ 两个位置的干草堆要交换位置，必须满足 $\max(a_x, a_x + (a_y - b_x)) \le \max(a_y, a_y + (a_x - b_y))$，即让交换后要操作 $2$ 次的干草堆数量减小。
	- 当 $a_i \le b_i$ 即 $a_i - b_i \le 0$ 时，所以 $\max(a_x, a_x + (a_y - b_x)) \le \max(a_y, a_y + (a_x - b_y))$ 等价于 $a_x \le a_y$。
	- 当 $a_i > b_i$ 即 $a_i - b_i > 0$ 时，所以 $\max(a_x, a_x + (a_y - b_x)) \le \max(a_y, a_y + (a_x - b_y))$ 等价于 $b_x > b_y$。
4. 统计答案时只需要记录需要进行两次操作的干草堆数量再加上所有干草堆的数量 $\sum a$。
5. 统计两次操作的干草堆数量方法为，枚举 $1 \le i \le n$，将第 $p_i$ 个干草堆作为最后清空的干草堆，需要进行 $2$ 次操作的干草堆数量为
	- $[1, i - 1]$ 每一堆干草堆清空操作时，需要进行 $2$ 次操作的干草堆的数量以及
	- 删除第 $i$ 个干草堆以后，剩余干草堆按顺序消除时，消除到每一堆时需要进行 $2$ 次操作的干草堆的数量的最大值。
6. 维护一个前缀 $pre_i = \max(pre_{i - 1}, prea_i - preb_{i - 1})$，以及一个后缀 $suf_i = \max (suf_{i + 1}, prea_{i + 1} - preb_i)$。当 $\sum b - b_i \ge \sum a$ 时， $ans = \sum a + \min_{i = 1}^{n}\max(pre_i, suf_i - (a_{p_i} - b_{p_i}), 0)$。

时间复杂度： $O(n\log n)$。