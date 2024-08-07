# [ABC 227 D] Project Planning

## 题面翻译

一个公司有 $N$ 个部门，第 $i$ 个部门有 $A_i$ 名员工。不会有员工同时属于多个不同部门。
公司计划建立若干个横跨不同部门的项目，一个项目必须由来自不同部门的恰好 $K$ 名员工组成。
问最多可以同时建立多少个项目？

## 题目描述

[problemUrl]: https://atcoder.jp/contests/abc227/tasks/abc227_d

キーエンスには $ N $ 個の部署があり、$ i\, (1\ \leq\ i\ \leq\ N) $ 番目の部署には $ A_i $ 人の社員が所属しています。異なる部署に同じ社員が所属していることはありません。

キーエンスは、部署をまたいだ全社横断プロジェクトを計画しています。$ 1 $ つのプロジェクトは $ K $ 個の相異なる部署から $ 1 $ 人ずつ選出して作り、ちょうど $ K $ 人から構成されるようにします。

プロジェクトは最大でいくつ作れますか？ただし、$ 1 $ 人が複数のプロジェクトに参加することはできません。

## 输入格式

入力は以下の形式で標準入力から与えられる。

> $ N $ $ K $ $ A_1 $ $ A_2 $ $ \ldots $ $ A_N $

## 输出格式

プロジェクトの個数の最大値を出力せよ。

## 样例 #1

### 样例输入 #1

```
3 3
2 3 4
```

### 样例输出 #1

```
2
```

## 样例 #2

### 样例输入 #2

```
4 2
1 1 3 4
```

### 样例输出 #2

```
4
```

## 样例 #3

### 样例输入 #3

```
4 3
1 1 3 4
```

### 样例输出 #3

```
2
```

## 提示

### 制約

- $ 1\ \leq\ K\ \leq\ N\ \leq\ 2\ \times\ 10^5 $
- $ 1\ \leq\ A_i\ \leq\ 10^{12} $
- 入力は全て整数

### Sample Explanation 1

$ 3 $ 個の部署それぞれから $ 1 $ 人ずつ選出したプロジェクトを $ 2 $ つ作ることができます。

## 题解
我们本题的思路同我们的洛谷 P 10184，我们先去二分我们的答案，然后我们每一次只需要去查看我们的当前的元素对应的天数是否是可行的，如果可行，我们一定满足我们的贡献之和大于我们的 $k*t$。

证明如下：
首先，一天至少会写 $t$ 种不同的科目。所以这 $x$ 天总共会至少写 $x\times t$ 个科目的题目。
 那么对于一种科目他最多写 $x$ 天。(每天都写这个科目)
 所以--种科目的题目他最多写 min $\{a_i,x\}$。

$$
\sum_{i=1}^{i=n}\min\{a_i,x\}\text{。}
$$
 那么这 $N$ 种科目最多可以写 $\sum_{i=1}^{i=n}\min\{a_i,x\}$。而这 $N$ 种科目最少得写 $x\times t$ 种题目。
 所以只需判断 $\sum_{i=1}^{i=n}\min\{a_i,x\}$ 是否大于等于 $x\times t$,是就是合法，不是就是非法。
```
#include <iostream>
using namespace std;
long long n,k,a[500500];
bool check(long long x){
	long long res=0;
	for(register long long i=1; i<=n; i++) 
		res+=min(a[i],x);
	return res<x*k;
}
signed main(){
	cin >> n >> k;
	for(register long long i=1; i<=n; i++)
		cin >> a[i];
	long long l=1,r=1e18/k,mid; 
	while(l<r){
		mid=(l+r)>>1;
		if(check(mid)) r=mid;
		else l=mid+1;
	}
	cout << l-1 << endl; 
	return 0;
}

```
