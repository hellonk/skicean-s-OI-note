# Problem
![image](https://github.com/hellonk/hellonk.github.io/blob/master/problem_1147_7f9476c07e337f99ca8a2cfce2dbd8f0.jpg)
# Solution
肯定可以DP，因为只能从上层往下层走，所以一定没有后效性。
关键问题就在于如何求出第*i*块木板可以往那一块木板上跳
数据范围*n=1e5*
所以怎么办呢？我们开一个线段树，然后就差不多了。
```cpp
#include <cstdio>
#include <algorithm>
#include <iostream>
#include <map>
#include <vector>
#include <cstring>
using namespace std;
const int N = 1e5 + 10;
long long inf = 9223372036854775807;
struct Node
{
	int val, lazy;			//不存储 l,r 的线段树是不用 build 的
	#define val(p) tree[p].val
	#define laz(p) tree[p].lazy
} tree[N<<2];
long long min (long long x, long long y)
{
	if (x > y)	return y;
	else	return x;
}
void spread (int p)
{
	if (laz(p))
	{
		val(p<<1) = laz(p);
		val(p<<1|1) = laz(p);
		laz(p<<1) = laz(p);
		laz(p<<1|1) = laz(p);
		laz(p) = 0;
	}
}
int ask (int p, int l, int r, int targ)
{
	if (l == targ && r == targ)
		return val(p);
	int mid = (l+r) >> 1;
	spread (p);
	if (targ <= mid)
		return ask (p<<1, l, mid, targ);
	else	return ask (p<<1|1, mid+1, r, targ);
}
void change (int p, int l, int r, int tarL, int tarR, int tar)
{
	if (tarL <= l && r <= tarR)
	{
		val(p) = tar;
		laz(p) = tar;
		return;
	}
	spread (p);
	int mid = (l+r)>>1;
	if (tarL <= mid)
		change (p<<1, l, mid, tarL, tarR, tar);
	if (tarR > mid)	change (p<<1|1, mid+1, r, tarL, tarR, tar);
}
struct floor
{
	int l, r, h;
} board[N];
int n, Maxn, all[N<<1];
map <int, int> disper;			//dispersed
bool cmp (floor x, floor y)
{
	return x.h < y.h;
}
int jump[N][2];
long long f[N][2];
int main ()
{
	scanf ("%d %d", &n, &Maxn);
	for (int i = 1; i <= n; ++i)
	{ 
		scanf ("%d %d %d", &board[i].h, &board[i].l, &board[i].r);
		all[2*i-1] = board[i].l;
		all[2*i] = board[i].r;
	}
	int begL = board[1].l, begR = board[1].r, begH = board[1].h, pos;
	sort (board+1, board+n+1, cmp);
	sort (all+1, all+2*n+1);
	for (int i = 1; i <= n; ++i)
		if (board[i].l == begL && board[i].r == begR && board[i].h == begH)
		{
			pos = i;
			break;
		}
	int now = 0;
	for (int i = 1; i <= 2*n; ++i)
	{
		if (all[i] == all[i-1])
			disper[all[i]] = disper[all[i-1]];
		else	disper[all[i]] = ++now;
	}
	map<int, int>::iterator it = disper.end();--it;
	int SegMax = it->second;
	for (int i = 1; i <= n; ++i)
	{
		jump[i][0] = ask (1, 1, SegMax, disper[board[i].l]);
		jump[i][1] = ask (1, 1, SegMax, disper[board[i].r]);
		change (1, 1, SegMax, disper[board[i].l], disper[board[i].r], i);
		f[i][0] = inf;
		f[i][1] = inf;
	}
	f[pos][0] = 0, f[pos][1] = f[pos][0] + board[pos].r - board[pos].l;
	long long ans = inf;
	for (int i = n; i >= 1; --i)
		if (board[i].h <= begH)
		{
			if (board[i].h - board[jump[i][0]].h <= Maxn && f[i][0] != inf)
			{
				if (jump[i][0] == 0)
					ans = min (ans, f[i][0]);
				else
				{
					f[jump[i][0]][0] = min (f[jump[i][0]][0], f[i][0] + board[i].l - board[jump[i][0]].l);
					f[jump[i][0]][1] = min (f[jump[i][0]][1], f[i][0] + board[jump[i][0]].r - board[i].l);
				}
			}
			if (board[i].h - board[jump[i][1]].h <= Maxn && f[i][1] != inf)
			{
				if (jump[i][1] == 0)
					ans = min (ans, f[i][1]);
				else
				{
					f[jump[i][1]][0] = min (f[jump[i][1]][0], f[i][1] + board[i].r - board[jump[i][1]].l);
					f[jump[i][1]][1] = min (f[jump[i][1]][1], f[i][1] + board[jump[i][1]].r - board[i].r);
				}
			}
		}
	printf ("%lld\n", ans);
	return 0;
}
```
