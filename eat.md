## problem two
### problem description
求用1 * 2的多米诺骨牌覆盖n * m 的情况总数
### Input format
输入一共一行，
第一行有两个整数，n和m。
### Output format
输出一共一行，
表示用1 * 2的多米诺骨牌覆盖n * m 的情况总数
### The scope of data
for 10% data, n<=1000000,m=2
for 30% data, n<=2^63-1,m=2
for 50% data, n<=50,m<=5
for 100% data, n<=2^63-1,m<=5
### solve
观察数据，发现最大的有2^63-1那么大，于是显然是一个 *logn* 的做法，就是矩阵乘法。先看前%40数据，n<=1000000和n<=2^63-1的数据，m都等于2。所以就可以用矩阵求斐波那契数列的第n项。然后第二个问题，就是在n<=50,m<=5的数据范围里面。怎么办呢？用状态压缩DP，f[i][j]表示前i行第i行的状态为j，j就是一个二进制数。转移方程嘛，f[i][j]只和f[i-1][k]有关系，转移一下即可。

不加矩阵快速幂的状压代码：
```cpp
#include <cstdio>
using namespace std;
const int N = 100, M = 5;
int n, m, f[N][1<<M];
bool judge (int state)
{
	int lianxu = 0, p = 0;
	bool a[20];
	for (int i = 1; i <= 10; ++i)
		a[i] = 0;
	while (state)
	{
		a[++p] = state & 1;
		state >>= 1;
	}
	for (int i = 1; i <= 10; ++i)
	{
		if (a[i] == 1)
			lianxu++;
		else
		{
			if ((lianxu & 1) == 1)
				return false;
		}
	}
	return true;
}
int main ()
{
	scanf ("%d %d", &n, &m);
	if (n < m)
		n ^= m ^= n ^= m;
	for (int i = 0; i < (1<<m); ++i)
		if (judge (i))
			f[1][i] = 1;
	for (int i = 1; i <= n-1; ++i)
		for (int j = 0; j < (1<<m); ++j)
		{
			int state = (1<<m)-1-j;
			for (int k = 0; k < (1<<m); ++k)
				if ((state|k) == k && judge (k-state))
					f[i+1][k] = (f[i+1][k] + f[i][j]) % 340340;
		}
	printf ("%d\n", f[n][(1<<m)-1]);
	return 0;
}
```

既然要用矩阵快速幂，那么我们就需要把那几个矩阵都初始化出来.关于矩阵快速幂，我就不讲了......下面是初始化矩阵的代码

```cpp
#include <cstdio>
#include <map>
#include <set>
using namespace std;
const int N = 100, M = 5;
int n, m, f[N][1<<M];
bool judge (int state)
{
	int lianxu = 0, p = 0;
	bool a[20];
	for (int i = 1; i <= 10; ++i)
		a[i] = 0;
	while (state)
	{
		a[++p] = state & 1;
		state >>= 1;
	}
	for (int i = 1; i <= 10; ++i)
	{
		if (a[i] == 1)
			lianxu++;
		else
		{
			if ((lianxu & 1) == 1)
				return false;
		}
	}
	return true;
}
map<int,set<int> > nb;
bool matrix[N][N];
int main ()
{
	freopen ("code.out", "w", stdout);
	scanf ("%d %d", &n, &m);
	if (n < m)
		n ^= m ^= n ^= m;
	for (int i = 0; i < (1<<m); ++i)
		if (judge (i))
			f[1][i] = 1;
	for (int i = 1; i < n; ++i)
		for (int j = 0; j < (1<<m); ++j)
		{
			int state = (1<<m)-1-j;
			for (int k = 0; k < (1<<m); ++k)
				if ((state|k) == k && judge (k-state))
				{
					f[i+1][k] = (f[i+1][k] + f[i][j]) % 340340;
					nb[k].insert (j);
					printf ("i=%d j=%d i+1=%d k=%d\n", i, j, i+1, k);
				}
		}
//	printf ("%d\n", f[n][(1<<m)-1]);
	for (map<int,set<int> >::iterator it = nb.begin (); it != nb.end (); ++it)
		for (set<int>::iterator itit = it->second.begin (); itit != it->second.end (); ++itit)
			matrix[(*itit)][(it->first)] = 1;
	printf ("{");
	for (int i = 0; i < (1<<m); ++i)
	{
		printf ("{");
		for (int j = 0; j < (1<<m); ++j)
		{
			if (j==(1<<m)-1)
				printf ("%d", matrix[i][j]);
			else
				printf ("%d,", matrix[i][j]);
		}
		printf ("}");
		if (i==(1<<m)-1)
			break;
		printf ("\n");
	}
	printf ("}");
	return 0;
}
```
