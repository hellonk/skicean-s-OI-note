# problem
![image](https://github.com/hellonk/hellonk.github.io/blob/master/problem_1162_92ee95a663e4db1741fc590a3f13c11f.jpg)
# solution
按照我的思考方式，先考虑一个点距离已知的n个点的距离最小是多少（距离是哈密顿距离）。
转化成数学题，设这n个点的坐标分别为 (x1, y1),(x2, y2), ......, (xn, yn)。
那么对于一个点来说，这个点的消耗就应该是 |a-x1|+|a-x2|+|a-x3|+......+|a-xn|+|b-y1|+|b-y2|+|b-y3|+......+|b-yn|
我们要让这个值尽量的小，对吧。所以我们就可以用一些数学方法就可以知道 a的值为 x1~xn的中位数的时候最优，b也同样。
我们可以计算出把所有n个小卖部都吸过来消耗的总内力最小是多少。吸走n-1个小卖部就是比吸走n个小卖部少吸走一个。
考虑不吸哪一个小卖部。