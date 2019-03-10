# Problem
![image](https://github.com/hellonk/hellonk.github.io/blob/master/problem_1147_7f9476c07e337f99ca8a2cfce2dbd8f0.jpg)
# Solution
肯定可以DP，因为只能从上层往下层走，所以一定没有后效性。
关键问题就在于如何求出第*i*块木板可以往那一块木板上跳
数据范围*n=1e5*
所以怎么办呢？我们现在开一个线段树。
