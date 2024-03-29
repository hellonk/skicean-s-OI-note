# Problem
[题目叙述](http://124.205.120.153/problem/1143)
# Solve
> ## 20分
> 我考试的时候想出来的方法就是一个很显然的dp
设f[i][j]为有i项，所有a[i]都比j小的时候数列的个数。
则f[i][j] = f[i-1][0]+f[i-1][1]+f[i-1][2]+......+f[i-1][j]
但是我们发现这个dp是O(n^3)的
> ## 20分的优化
> 可以优化，但是如果不写高精度的话，得不了更高的分数。
f[i-1][0]+......+f[i-1][j]可以用一个前缀和，在计算f[i][j]的时候先计算一下f[i-1][j]的前缀和就好了。
> ## 100分算法
> 我们发现这就是一个不断计算前缀和的过程。
考虑打表找规律。
```
1 2 3 4 5 6
1 3 6 10 15 21
1 4 10 20 ......
```
我们把这个东西斜着看，第i行的第j个数等于C(i, i+j-1)。j+1写在下面，i写在上面。
但是我没有明白这是为什么...就是找规律。
但是如何计算这个组合数呢？
高精度计算组合数。
C(i, j)应该是用质因数分解法。
C(i, j) = j*(j-1)*......*(j-i+1)/1/2/....../i 然而这个代数式是有除法的。而且要除很多的东西，容易爆炸，而且得写高精度除法。
考虑分解质因数，分解分子的质因数和分母的质因数。
在这个高精度里面，只需要一个高精度乘法（高精度乘以单精度）。
# The key of this problem
> 我们可以大概判断出来这道题的复杂度。基本 O(n)或者 O(n sqrt (n))和 O(nlogn)。
显然不是什么数据结构题，所以就是常数很大的 O(n)算法。
从问题出发分析，可以发现一个很显然的 O(n^3)的 dp问题。
我们的目标是 O(n)，但是我们如果用 dp手段的话，可以加一个前缀和优化，优化成 O(n^2)的算法。
但是肯定优化不成 O(n)的算法啊。
在这个时候就要打表找规律了，发现这恰好是一个高精度求组合数的问题。
