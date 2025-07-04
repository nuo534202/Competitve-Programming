<h1 style="text-align:center;">Daily Practice</h1>

# 6.16

## 1. 洛谷 P1043 [NOIP 2003 普及组] 数字游戏

**题目链接**：[洛谷 P1043](https://www.luogu.com.cn/problem/P1043)

**题意**：将 $n$ ($n \le 50$) 个环形数字分成 $m$ ($m \le 9$) 组，求每一组和在模 $10$ 后乘积的最小值和最大值。

**错误解法和代码**

利用 `dfs` 找出每一个分组的边界，再对数字维护一个前缀和，暴力求出每一种分组方案的结果。由于这种做法的时间复杂度是 $O(n^m)$ 最后导致了 `TLE`。

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, m, a[55], pre[55], maxm = 0, minm = INT_MAX;

void dfs(int cur, vector<int>& v) {
    if (v.size() >= m) {
        int res = (pre[v[0]] + pre[n] - pre[v.back()] + 1000000) % 10;
        for (int i = 1; i < v.size(); i++)
            res *= ((pre[v[i]] - pre[v[i - 1]] + 1000000) % 10);
        maxm = max(maxm, res), minm = min(minm, res);
        return;
    }
    if (cur >= n) return;
    for (int i = cur + 1; i <= n; i++) {
        v.push_back(i);
        dfs(i, v);
        v.pop_back();
    }
}

int main() {
    ios::sync_with_stdio(false); cin.tie(0); cout.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++) cin >> a[i];
    for (int i = 1; i <= n; i++) pre[i] = pre[i - 1] + a[i];
    vector<int> v;
    dfs(0, v);
    cout << minm << endl << maxm << endl;
    return 0;
}
```

**正确解法和代码**

区间 $DP$：将环形数字倍长之后可以将其当作线形数字来处理。设 $maxm_{l, r, k}$, $minm_{l, r, k}$ 分别为 $[l, r]$ 区间内，将线性数字分为 $k$ 段的最大、最小值，具体状态转移方程看代码。

时间复杂度： $O(n^3m)$。

```cpp
#include <bits/stdc++.h>
using namespace std;

#define int long long

int n, m, a[105], maxm[105][105][10], minm[105][105][10];

signed main() {
    ios::sync_with_stdio(false); cin.tie(0); cout.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++) cin >> a[i], a[i + n] = a[i];
    for (int i = 1; i <= 2 * n; i++) a[i] += a[i - 1];
    for (int i = 1; i <= 100; i++)
		for (int j = 1; j <= 100; j++)
			for (int k = 0; k < 10; k++) maxm[i][j][k] = 0, minm[i][j][k] = 1e9;
    // cout << minm[0][0][0];
    for (int i = 1; i <= 2 * n; i++)
        for (int j = 1; j <= 2 * n; j++) {
            maxm[i][j][1] = minm[i][j][1] = ((a[j] - a[i - 1]) % 10 + 10) % 10;
		}
    for (int len = 2; len <= n; len++) {
        for (int l = 1; l <= n; l++) {
			int r = l + len - 1;
            for (int k = 2; k <= m; k++) {
				if (len < k) continue;
                for (int p = l; p < r; p++) {
                    maxm[l][r][k] = max(maxm[l][r][k], maxm[l][p][k - 1] * maxm[p + 1][r][1]);
                    minm[l][r][k] = min(minm[l][r][k], minm[l][p][k - 1] * minm[p + 1][r][1]);
                }
            }
        }
    }
    int ans1 = LLONG_MAX, ans2 = 0;
    for (int i = 1; i <= n; i++)
        ans1 = min(ans1, minm[i][i + n - 1][m]), ans2 = max(ans2, maxm[i][i + n - 1][m]);
	// for (int i = 1; i <= n; i++) cout << minm[i][i + n - 1][m] << " ";
    cout << ans1 << endl << ans2 << endl;
    return 0;
}
```

## 2. P1121 环状最大两段子段和

**题目链接**：[洛谷 P1121](https://www.luogu.com.cn/problem/P1121)

**题意**：给出一段长度为 $n$ 的环形序列，选出两个不重合的非空段，使得两段的和最大。

**错误解法及代码**

将环形序列倍长当成线性序列处理。设 $dp_{i, 0/1/2}$ 表示以 $i$ 结尾选取了 $0/1/2$ 个子段的最大和。状态转移方程为 $dp_{i, 1} = \max(dp_{i - 1, 1} + a_i, a_i)$， $dp_{i, 2} = \max(dp_{i - 1, 2} + a_i, \max_{j = l}^{i - 1}dp_{j, 1} + a_i)$。时间复杂度为 $O(n^2)$ 导致了 `TLE`，并且可能做法或者代码导致有误导致了 `WA`。

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 4e5 + 5;
int n, a[N], dp[N][3], maxm[N], ans = 0;

int main() {
	ios::sync_with_stdio(false); cin.tie(0); cout.tie(0);
	cin >> n;
	for (int i = 1; i <= n; i++) cin >> a[i], a[i + n] = a[i];
	for (int l = 1; l <= n; l++) {
		maxm[l] = dp[l][1] = a[l], dp[l][0] = dp[l][2] = 0;
		for (int i = l + 1; i <= l + n - 1; i++) {
			dp[i][1] = max(dp[i - 1][1] + a[i], a[i]);
			dp[i][2] = max({dp[i - 1][2] + a[i], maxm[i - 1] + a[i], dp[i - 1][1] + a[i]});
			maxm[i] = max(maxm[i - 1], dp[i][1]);
			ans = max(ans, dp[i][2]);
		}
	}
	cout << ans << endl;
	return 0;
}
```

**正确解法及代码**

对于一个长度为 $n$ 的环形序列，选取两个最大子段使其和最大，只有可能有以下两种选法（用 $1$ 表示选择对应元素， $0$ 表示不选对应元素）：

1. 00011110000111100
2. 111000011110000111

对于第 $1$ 种情况，直接利用 $dp$ 分别处理出前缀、后缀的最大子段和，然后枚举分界点找出两段和的最大值即可。对于第二种情况，我们可以不找 $1$ 的部分，去找 $0$ 的部分，也就是将原序列全部变为相反数后，再找两段最大的子段和，然后再将该结果加上原序列的总和即为答案。

**特判**：整个序列只有一个正数时，答案就是序列中两个最大的值的和。

时间复杂度： $O(n)$。

```cpp
#include <bits/stdc++.h>
using namespace std;

#define int long long

const int N = 2e5 + 5;
int n, a[N], pre[N], suf[N], sum = 0, num = 0;

int query() {
	for (int i = 1; i <= n; i++) pre[i] = max(pre[i - 1], 0LL) + a[i];
	for (int i = 2; i <= n; i++) pre[i] = max(pre[i], pre[i - 1]);
	for (int i = n; i; i--) suf[i] = max(suf[i + 1], 0LL) + a[i];
	for (int i = n - 1; i; i--) suf[i] = max(suf[i], suf[i + 1]);
	int res = LLONG_MIN;
	for (int i = 1; i < n; i++) res = max(res, pre[i] + suf[i + 1]);
	return res;
}

signed main() {
	ios::sync_with_stdio(false); cin.tie(nullptr); cout.tie(nullptr);
	cin >> n;
	for (int i = 1; i <= n; i++)
		cin >> a[i], sum += a[i], num += (a[i] > 0);
	int res1 = query();
	if (num == 1) cout << res1 << endl;
	else {
		for (int i = 1; i <= n; i++) a[i] = -a[i];
		int res2 = sum + query();
		if (!res2) res2 = LLONG_MIN;
		cout << max(res1, res2) << endl;
	}
	return 0;
}
```

## 3. Codeforces Round 1031 A. Shashliks

**题目链接**：[Codeforces Round 1031 A](https://codeforces.com/contest/2113/problem/A)

**题意**：给定一个初始温度 $k$，当温度大于等于 $a$ 时，可以进行第一种操作使温度下降 $x$ 度；当温度大于等于 $b$ 时，可以进行第二种操作使温度下降 $y$ 度。求最多操作次数。

**正确思路及代码**

贪心。

不妨假设 $a > b$ ($a \le b$ 时分别交换 $(a, b)$ 和  $(x, y)$ 即可)

- 当 $x > y$ 时，一直进行第二种操作是最优的操作。
- 当 $x < y$ 时，先进行第一种操作直到温度 $k < a$ 再进行第二种操作是最优的操作。

时间复杂度： $O(1)$。

```cpp
#include <bits/stdc++.h>
using namespace std;

#define int long long

void solve() {
    int k, a, b, x, y;
    cin >> k >> a >> b >> x >> y;
    if (a < b) swap(a, b), swap(x, y);
    int res = 0;
    if (x < y) {
        int res1 = max(0LL, (k - a + x) / x), res2 = max(0LL, (k - res1 * x - b + y) / y);
        res = max(res1 + res2, res);
    } else {
        res = max(res, (k - b + y) / y);
    }
    cout << res << "\n";
}

signed main() {
    ios::sync_with_stdio(false); cin.tie(nullptr); cout.tie(nullptr);
    int T;
    cin >> T;
    while (T--) solve();
    return 0;
}
```

## 4. Codeforces Round 1031 B. Good Start

**题目链接**：[Codeforces Round 1031 B](https://codeforces.com/contest/2113/problem/B)

**题意**：平面内给定一个从 $(0, 0)$ 到 $(w, h)$ 的矩形，需要用大小为 $a \times b$ 纸张去覆盖整个矩形，纸张可以超出矩形边界，但是不能旋转、不能重叠。现在矩形内已经放有两张纸，问是否能在不移动这两张纸的前提下，按照要求用纸张覆盖整个矩形。

**正确思路及代码**

当 $(x_1 - x_2) \% a = 0$ 或 $(y_1 - y_2) \% b = 0$ 时输出 `Yes`。

**特判**：当 $x_1 = x_2$ 或 $y_1 = y_2$ 时需要判断另一个不相等的坐标的差是否能整除 $a$ 或者 $b$。

时间复杂度： $O(1)$。

```cpp
#include <bits/stdc++.h>
using namespace std;

#define int long long

int w, h, a, b, x1, Y1, x2, y2;

void solve() {
    cin >> w >> h >> a >> b >> x1 >> Y1 >> x2 >> y2;
    if (x1 == x2) {
        if (abs(Y1 - y2) % b == 0) cout << "Yes\n";
        else cout << "No\n";
    } else if (Y1 == y2) {
        if (abs(x1 - x2) % a == 0) cout << "Yes\n";
        else cout << "No\n";
    } else {
        if ((x1 - x2) % a == 0 || (Y1 - y2) % b == 0) cout << "Yes\n";
        else cout << "No\n";
    }
}

signed main() {
    ios::sync_with_stdio(false); cin.tie(nullptr); cout.tie(nullptr);
    int T;
    cin >> T;
    while (T--) solve();
    return 0;
}
```

## 5. Codeforces Round 1031 C. Smilo and Minecraft

**题目链接**：[Codeforces Round 1031 C](https://codeforces.com/contest/2113/problem/C)

**题意**：给出大小为 $n \times m$ ($n, m \le 500$)的矿区，每一个格子可以是金矿 ($g$)、石头 (#) 或者是空地 ($.$)。收集金矿的方法为在一个空地安放炸药，以炸药为中心边长为 $2k + 1$ ($k \le 500$) 的正方形内，最边缘的金矿可以被收集，其余位置全部变为空地。问最多能收集多少金矿。

**正确思路及代码**

可以发现无论 $k$ 等于多少，第二次操作开始一定不会损失金矿，所以第一次操作只需要选择损失金矿最少的空地进行操作即可。为了快速查询区间内的金矿数量，可以利用二维前缀和对金矿数量进行维护。

时间复杂度： $O(nm)$。

```cpp
#include <bits/stdc++.h>
using namespace std;

#define int long long

int n, m, k, pre[505][505];

void solve() {
	cin >> n >> m >> k;
	string s[505];
	for (int i = 1; i <= n; i++) cin >> s[i], s[i] = " " + s[i];
	for (int i = 0; i <= n; i++)
		for (int j = 0; j <= m; j++) pre[i][j] = 0;
	for (int i = 1; i <= n; i++)
		for (int j = 1; j <= m; j++)
			pre[i][j] = pre[i - 1][j] + pre[i][j - 1] - pre[i - 1][j - 1] + (s[i][j] == 'g');
	int res = INT_MAX;
	for (int i = 1; i <= n; i++)
		for (int j = 1; j <= m; j++)
			if (s[i][j] == '.') {
				int maxx = min(n, i + k - 1), maxy = min(m, j + k - 1);
				int minx = max(1LL, i - k + 1), miny = max(1LL, j - k + 1);
				int tmp = pre[maxx][maxy] - pre[minx - 1][maxy] - pre[maxx][miny - 1] + pre[minx - 1][miny - 1];
				res = min(res, tmp);
			}
	cout << pre[n][m] - res << "\n";
}

signed main() {
    ios::sync_with_stdio(false); cin.tie(nullptr); cout.tie(nullptr);
    int T;
    cin >> T;
    while (T--) solve();
    return 0;
}
```
