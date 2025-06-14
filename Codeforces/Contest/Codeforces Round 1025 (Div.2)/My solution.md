比赛链接：[CF1025](https://codeforces.com/contest/2109)

# A. It's Time To Duel

只有 $A$ 数组全是 $0$、全是 $1$ 或者有两个连续的 $0$ 才能确定有人说谎（一场比赛至少有一个胜者），否则不能确定有人说谎。

时间复杂度： $O(n)$。

# B. Slice to Survive

从第二次分割开始 Fouad 一定会跳到最中间的格子让每次减去的格子尽可能地少，Mouf 每次最多只能将剩余格子的长或宽减少一半。假如第二次剩余 $n' \times m'$ 的格子，那么剩余操作次数就是 $\lceil \log_2n'\rceil + \lceil \log_2m' \rceil$。

时间复杂度： $O(1)$。

# C1. Hacking Numbers (Easy Version)

```cpp
#include <bits/stdc++.h>
using namespace std;

int solve(int i) {
    int res = 0;
    while (i) res += (i % 10), i /= 10;
    return res;
}

int main() {
    ios::sync_with_stdio(false); cin.tie(nullptr); cout.tie(nullptr);
	int res = 0;
    for (int i = 1; i <= 1000000000; i++) {
        int tmp = solve(i);
        tmp = solve(tmp);
        res = max(res, tmp);
    }
    cout << res << endl;
    return 0;
}


```

通过上面这个程序可以发现，$[1, 10^9]$ 范围内的所有数通过两次 `digit` 操作都会收敛到 $[1, 16]$ 的范围内。

1. 执行操作 `add -8`，那么当前 $x$ 就会在 $[1, 8]$ 的范围内。
2. 执行操作 `add -4`，那么当前 $x$ 就会在 $[1, 4]$ 的范围内。
3. 执行操作 `add -2`，那么当前 $x$ 就会在 $[1, 2]$ 的范围内。
4. 执行操作 `add -1`，那么当前 $x$ 一定是 $1$。
5. 执行 `add n-1`，那么 $x$ 一定等于 $n$。

时间复杂度： $O(1)$。

# C2. Hacking Numbers (Medium Version)

1. 进行 `mul 9` 操作后， $x$ 一定会变成 $9$ 的倍数。
2. 进行 `digit` 操作后， $x$ 一定也还是 $9$ 的倍数，而且最多是两位数。
3. 再次进行 `digit` 操作后， $x$ 一定是 $9$。
4. 进行 `add n-9` 操作后， $x$ 就等于 $n$。

时间复杂度： $O(1)$。
