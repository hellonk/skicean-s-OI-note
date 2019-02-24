# Noip Simulation match on 2019.2.22
## problem one
### problem description
This problem is a very simple problem. Once upon a time, there was a very fat man. He wanted to find some food to eat then. He knew there was a lot of food in the end of the corridor. But there was a lot of obstacles in the corridor. He couldn't go through the obstacles easily. But he needed to go to the kitchen too. He could change his size. We can estimate 4this corridor as a plane rectangular coordinate system. We can estimate the left wall of the corridor as a line *y = 0* in the system. We can also estimate the right wall of the corridor as a line *y = m* in the system. The obstacles can be estimated as a point *(x, y)* in the system. The fat person can estimate as a circular. Now we need to now the radius of the maximum of the radium of the circular if it can went to the kitchen. 

### Input format
The first line of the input file are two number *n* of the obstacles and a number *m*. The mean of *m* is in the problem description. In the next *n* line, there is two number *x* and *y* every line. They are all coordinates of the obstacles. 

### Output format
There is only a number in the output file. Now we need to now the radius of the maximum of the radium of the circular if it can went to the kitchen. 

### The scope of data
for 30% data, n<=30
for 60% data, n<=1000
for 100% data, n<=3000

### solve
My English is so bad, so I need to write the solution in Chinese
考试的时候这道题其实已经想出来了qwq.
这道题最容易想到的就是二分.二分最大的直径，然后我们把所有的使得直径为这个数的两个点用并查集合并.把上界*y=0*和*y=m*都想成一个点，设这两个点的编号是*n+1*和*n+2*,如果一个在走廊内部的点和*n+1*距离比*m*小的话，那么就把那个点和*n+1*用并查集合并，当上界和下界都连成一气的时候圆就过不去了.复杂度 *O((n^2)logn)* 但是不知道为什么过不去......可能是多了个*log*.然后关于正解......我们把所有边都加入，然后我们发现肯定是过得了距离大的两对点，过不了距离小的两个点.所以我们可以把任意两个点连一条边，然后排序.我们把边按从小到大的顺序加入，有边的两个点是那个球通过不了的.直到上界和下界可以互相到达，这样的话球就过不了墙了.答案就是最后加的那条边的权值......但是依旧过不了......所以题解就过不了了？！然而并不是，我们发现最有这里的问题就相当于求一个所有生成树中的最大边最小......不不不，是相当于求能把上界和下界连起来的所有边中的最大值最小，然后我们的方法就相当于kruskal算法，复杂度是 O((n^2)logn)，因为中间需要排序......

kruskal
```cpp
#include <cstdio>
#include <cmath>
#include <algorithm>
using namespace std;
const int N = 3e3 + 10, maxn = 1e6;
char buffer[maxn],*S,*T; 
inline char Get_Char()  
{  
    if(S==T)  
    {  
        T=(S=buffer)+fread(buffer,1,maxn,stdin);
        if(S==T) return EOF;  
    }  
    return *S++;  
}

inline int Get_Int()
{
    char c;  
    int re=0;  
    for(c=Get_Char();c<'0'||c>'9';c=Get_Char());  
    while(c>='0'&&c<='9')  
           re=(re<<1)+(re<<3)+(c-'0'),c=Get_Char();  
    return re;  
}
struct point
{
	int x, y;
} a[N];
struct Node
{
	int u, v;
	double len;
} edge[N*N];
inline bool cmp (Node a, Node b)
{
	return a.len < b.len;
}
int n, m, tot;					//n+1和n+2分别是上界和下界
int fa[N], sze[N];
inline void init ()
{
	for (int i = 1; i <= n+2; ++i)
		fa[i] = i, sze[i] = 1;
}
inline int find (int x)
{
	if (fa[x] == x)
		return x;
	return fa[x] = find (fa[x]);
}
inline void merge (int x, int y)
{
	x = find (x), y = find (y);
	if (x == y)
		return;
	if (sze[x] < sze[y])
	{
		fa[x] = y;
		sze[y] += sze[x];
		sze[x] = 0;
	}
	else
	{
		fa[y] = x;
		sze[x] += sze[y];
		sze[y] = 0;
	}
}
inline double dist (point x, point y)
{
	return sqrt (1LL*(x.x-y.x)*(x.x-y.x) + 1LL*(x.y-y.y)*(x.y-y.y));
}
int main ()
{
	n = Get_Int (), m = Get_Int ();
	for (int i = 1; i <= n; ++i)
		a[i].x = Get_Int (), a[i].y = Get_Int ();
	for (int i = 1; i < n; ++i)
		for (int j = i+1; j <= n; ++j)
		{
			edge[++tot].u = i;
			edge[tot].v = j;
			edge[tot].len = dist (a[i], a[j]);
		}
	for (int i = 1; i <= n; ++i)
	{
		edge[++tot].u = i;
		edge[tot].v = n+1;
		edge[tot].len = m-a[i].y;
		edge[++tot].u = i;
		edge[tot].v = n+2;
		edge[tot].len = a[i].y;
	}
	sort (edge+1, edge+tot+1, cmp);
	init ();
	for (int i = 1; i <= tot; ++i)
	{
		merge (edge[i].u, edge[i].v);
		if (find (n+1) == find (n+2))
		{
			printf ("%.2lf\n", edge[i].len/2);
			return 0;
		}
	}
	return 0;
}
```
BinarySearch（二分查找）
```cpp
#include <cstdio>
#include <cmath>
using namespace std;
const int N = 3e3 + 10;
struct point
{
	int x, y;
} a[N];
int n, m;					//n+1和n+2分别是上界和下界
int fa[N];
void init ()
{
	for (int i = 1; i <= n+2; ++i)
		fa[i] = i;
}
int find (int x)
{
	if (fa[x] == x)
		return x;
	return fa[x] = find (fa[x]);
}
void merge (int x, int y)
{
	x = find (x), y = find (y);
	if (x != y)
		fa[x] = y;
}
double dist (point x, point y)
{
	return sqrt (1LL*(x.x-y.x)*(x.x-y.x) + 1LL*(x.y-y.y)*(x.y-y.y));
}
bool check (double r)
{
	init ();
	for (int i = 1; i < n; ++i)
		for (int j = i+1; j <= n; ++j)
			if (dist (a[i], a[j]) < r)
				merge (i, j);
	for (int i = 1; i <= n; ++i)
		if (m-a[i].y < r)
			merge (i, n+1);
	for (int i = 1; i <= n; ++i)
		if (a[i].y < r)
			merge (i, n+2);
	if (find (n+1) == find (n+2))
		return false;
	else	return true;
}
void binary_search ()
{
	double l = 0, r = (double)m;
	int tim = 0;
	while (tim++ <= 40)
	{
		double mid = (l + r) / 2;
		if (check (mid))
			l = mid;
		else	r = mid;
	}
	printf ("%.2lf\n", l/2);
}
int main ()
{
	scanf ("%d %d", &n, &m);
	for (int i = 1; i <= n; ++i)
		scanf ("%d %d", &a[i].x, &a[i].y);
	binary_search ();
	return 0;
}
```
然后就是Prim，Prim适用于稠密图，kruskal适用于稀疏图。然后我们发现图上就应该的是任意两个点是有边。所以我们应该用Prim法。
1. 输入：一个加权连通图，其中顶点集合为V，边集合为E；
2. 初始化：Vnew = {x}，其中x为集合V中的任一节点（起始点），Enew = {},为空；
3. 重复下列操作，直到Vnew = V：
    a.在集合E中选取权值最小的边<u, v>，其中u为集合Vnew中的元素，而v不在Vnew集合当中，并且v∈V（如果存在有多条满足前述条件即具有相同权值的边，则可任意选取其中之一）；
    b.将v加入集合Vnew中，将<u, v>边加入集合Enew中；
4. 输出：使用集合Vnew和Enew来描述所得到的最小生成树。
既然这道题任意两个点都有边，那么我们不需要用邻接表了，用邻接矩阵就好了
```cpp
#include <cstdio>
#include <cmath>
#include <algorithm>
#include <cstring>
using namespace std;
const int N = 3e3 + 10, maxn = 1e6;
double max (double x, double y)
{
	if (x > y)	return x;
	else 	return y;
}
double min (double x, double y)
{
	if (x < y)	return x;
	else	return y;
}
char buffer[maxn],*S,*T; 
inline char Get_Char()  
{  
    if(S==T)  
    {  
        T=(S=buffer)+fread(buffer,1,maxn,stdin);
        if(S==T) return EOF;  
    }  
    return *S++;  
}

inline int Get_Int()
{
    char c;  
    int re=0;  
    for(c=Get_Char();c<'0'||c>'9';c=Get_Char());  
    while(c>='0'&&c<='9')  
           re=(re<<1)+(re<<3)+(c-'0'),c=Get_Char();  
    return re;  
}
struct point
{
	int x, y;
} a[N];
int n, m;
double matrix[N][N];
inline double dist (point x, point y)
{
	return sqrt (1LL*(x.x-y.x)*(x.x-y.x) + 1LL*(x.y-y.y)*(x.y-y.y));
}
int vis[N];
double _Dist[N];
int main ()
{
	scanf ("%d %d", &n, &m);
	for (int i = 1; i <= n; ++i)
		scanf ("%d %d", &a[i].x, &a[i].y);
	for (int i = 1; i < n; ++i)
		for (int j = i+1; j <= n; ++j)
		{
			double dis = dist (a[i], a[j]);
			matrix[i][j] = dis;
			matrix[j][i] = dis;
		}
	for (int i = 1; i <= n; ++i)
	{
		matrix[n+1][i] = m-a[i].y;
		matrix[i][n+1] = m-a[i].y;
	}
	for (int i = 1; i <= n; ++i)
	{
		matrix[n+2][i] = a[i].y;
		matrix[i][n+2] = a[i].y;
	}
	matrix[n+1][n+2] = m;
	matrix[n+2][n+1] = m;
	for (int i = 1; i <= n+2; ++i)
		_Dist[i] = 2147483647.00, vis[i]=0;
	_Dist[n+1] = 0;
	int all = 0;
	double ans = 0;
	while (!vis[n+2])
	{
		double minn = 2147483647.00;
		int now = 0;
		for (int i = 1; i <= n+2; ++i)
			if (minn > _Dist[i] && (!vis[i]))
			{
				minn = _Dist[i];
				now = i;
			}
		vis[now] = 1;
		_Dist[now] = 0;
		ans = max (ans, minn);
		for (int i = 1; i <= n+2; ++i)
			if (!vis[i])
				if (_Dist[i] > matrix[now][i] && now != i)
					_Dist[i] = matrix[now][i];
	}
	printf ("%.2lf\n", ans/2);
	return 0;
}
```
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
观察数据，发现最大的有2^63-1那么大，于是显然是一个 *logn* 的做法，就是矩阵乘法
然后先看
