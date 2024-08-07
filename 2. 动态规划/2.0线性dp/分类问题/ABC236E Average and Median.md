#二分 #中位数 #平均数 
# [ABC 236 E] Average and Median

## 题面翻译

$n$ 个数 $a_{1}\dots a_n$ 排成一列。现在要选出一些数，满足 **任意两个相邻的数中至少有一个数被选择**。

请求出：

- 所有选择方案中，被选中的数字平均值的最大值，误差在 $10^{-3}$ 以内视为正确；

- 所有选择方案中，被选中的数字中位数的的最大值。在这里，偶数 $2k$ 个数的中位数视作第 $k$ 小的数。

- $2\leq n\leq 10^5$

- $1\leq a_i\leq 10^9$

## 题目描述

[problemUrl]: https://atcoder.jp/contests/abc236/tasks/abc236_e

$ N $ 枚のカードがあり、$ i\ \,\ (1\ \leq\ i\ \leq\ N) $ 番目のカードには整数 $ A_i $ が書かれています。

高橋君は、これらのカードから好きな枚数選びます。ただし、各 $ i\ \,\ (1\ \leq\ i\ \leq\ N\ -\ 1) $ について、$ i $ 番目のカードと $ i\ +\ 1 $ 番目のカードの少なくとも一方を選ぶ必要があります。

以下の値を求めてください。

- 選んだカードに書かれた整数の平均値としてあり得る最大値
- 選んだカードに書かれた整数の中央値としてあり得る最大値

ただし、$ n $ 個の整数の中央値は、それらのうち小さい方から数えて $ \lceil\ \frac{n}{2}\ \rceil $ 番目であるものとします。ここで、$ \lceil\ x\ \rceil $ は $ x $ 以上の最小の整数を表します。

## 输入格式

入力は以下の形式で標準入力から与えられる。

> $ N $ $ A_1 $ $ \ldots $ $ A_N $

## 输出格式

$ 2 $ 行出力せよ。$ 1 $ 行目には選んだカードに書かれた整数の平均値としてあり得る最大値を、$ 2 $ 行目には選んだカードに書かれた整数の中央値としてあり得る最大値を出力せよ。平均値の出力については、正しい値との相対誤差または絶対誤差が $ 10^{-3} $ 以下であれば正答とみなされる。

## 样例 #1

### 样例输入 #1

```
6
2 1 2 1 1 10
```

### 样例输出 #1

```
4
2
```

## 样例 #2

### 样例输入 #2

```
7
3 1 4 1 5 9 2
```

### 样例输出 #2

```
5.250000000
4
```

## 提示

### 制約

- $ 2\ \leq\ N\ \leq\ 10^5 $
- $ 1\ \leq\ A_i\ \leq\ 10^{9} $
- 入力は全て整数である。

### Sample Explanation 1

$ 2 $ 番目、$ 4 $ 番目、$ 6 $ 番目のカードを選ぶと、書かれた整数の平均は $ \frac{12}{3}\ =\ 4 $ となり、これが最大です。 $ 1 $ 番目、$ 3 $ 番目、$ 5 $ 番目、$ 6 $ 番目のカードを選ぶと、書かれた整数の中央値は $ 2 $ となり、これが最大です。

### Sample Explanation 2

平均値の出力については誤差が認められるので、例えば $ 5.2491 $ と出力しても正答とみなされます。ただし、中央値は正確な値を出力しなければなりません。

## 题解
我们这一题关键在于转化：
我们首先可以考虑二分我们的答案，即枚举我们的平均数是多少，然后再去计算，这里我们对于平均数的枚举有下面的技巧：假设我们平均数是 $x$,那么我们只需要构造我们的数组 $b[i]=a[i]-x$，然后求这个 $b[i]$ 数组中的**满足条件的和最大值**是否是大于 0 的，如果是，那么我们就认为满足，否则不满足。

同理，对于我们的中位数，我们可以考虑下面的性质：比中位数大的数字一定有至少 $\dfrac{k}{2}$ 个，比中位数小的数字一定有另外的 $\dfrac{k}{2}$ 个，那么我们此时可以构造：$b[i]=a[i]\geq mid?1:-1$,然后再去求我们 $b[i]$ 的**满足条件的最大和**，如果和大于 0，说明超过一般的大于 k，

```cpp
const int MAXN = 5e5 + 5;
int n;
template <class T> T maximize(const vector<T>& a) {
	vector<T>f(n);	
	f[0] = a[0], f[1] = max((T)0, a[0]) + a[1];
	for (int i = 2; i < a.size(); i++) {
		f[i] = max(f[i - 1], f[i - 2]) + a[i];
	}
	return max(f[n - 1], f[n - 2]);
}
int a[MAXN];
void slove() {
	cin >> n;
	for (int i = 1; i <= n; i++)cin >> a[i];
        //用括号是因为两个二分一个是double一个是int
	{
		double L = 0, R = INF;
		double ans = 0;
		while (abs(L - R) > eps) {
			double mid = (L + R) / 2;
			vector<double>b(n);
			for (int i = 1; i <= n; i++)b[i - 1] = a[i] - mid;
			if (maximize(b) >= 0.0) {
				L = mid;
				ans = max(ans, mid);
			}
			else {
				R = mid;
			}
		}
		baoliu(ans, 12) << endl;
	}

	{
		int L = 0, R = INF;
		int ans = 0;
		while (L <= R) {
			int mid = (L + R) / 2;
			vector<int>b(n);
			for (int i = 1; i <= n; i++)b[i - 1] = (a[i] >= mid ? 1 : -1);
			if (maximize(b) > 0) {
				L = mid + 1;
				ans = max(ans, mid);
			}
			else {
				R = mid - 1;
			}
		}
		cout << ans << endl;
	}
}
```