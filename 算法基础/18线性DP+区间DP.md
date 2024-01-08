## 动态规划的时间复杂度分析

$动态规划的时间复杂度 = 状态数量 \times 转移的计算量$

****

## 线性DP

### 1. 数字三角形

![image-20231104130427052](image\image-20231104130427052.png)

```
输入
5
7
3 8
8 1 0
2 7 4 4
4 5 2 6 5
输出
30

输入
10
-6
-4 -5
-3 7 5
3 7 -2 1
10 2 -6 2 -6
-8 3 8 6 7 9
-4 -10 0 -3 4 9 2
0 5 5 5 10 -6 -5 -4
-9 7 4 9 8 -5 -2 3 2
-7 -4 0 -10 -8 -4 3 -5 8 9
输出
25
```

- 思路

<img src="image\image-20231104130200105.png" alt="image-20231104130200105" style="zoom:50%;" />

1. 状态表示f[i,j]
   1. 集合：所有从起点，走到$(i,j)$的路径
   2. 属性：Max

2. 状态计算 —— 集合划分

分成两大类，一类来自左上方$f[i-1,j-1] + a[i, j]$，一类来自右上方$f[i-1,j] + a[i,j]$

所以，状态转移方程：$f[i,j]= max(f[i-1,j-1] + a[i, j], f[i-1,j] + a[i,j])$

- 时间复杂度分析：$状态数量O(n^2)\times 转移计算量O(1) = O(n^2)$

- 代码

```c++
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 510, INF = 0x3f3f3f3f;

int n, m;
int a[N][N];
int f[N][N];

int main()
{
	cin >> n;
	for (int i = 1; i <= n; i ++ )
		for (int j = 1; j <= i; j ++ )
			cin >> a[i][j];

	// 要先把f初始化成负无穷
	memset(f, -0x3f, sizeof f);

	f[1][1] = a[1][1];

	for (int i = 2; i <= n; i ++ )
		for (int j = 1; j <= i; j ++ )
			f[i][j] = max(f[i - 1][j - 1] + a[i][j], f[i - 1][j] + a[i][j]);

	int res = -INF;
	for (int j = 1; j <= n; j ++ ) res = max(res, f[n][j]);

	cout << res << endl;

	return 0;
}
```

****

### 2. [最长上升子序列](https://www.acwing.com/problem/content/897/)

![image-20231104133121472](image\image-20231104133121472.png)

```
输入
7
3 1 2 1 8 5 6
输出
4
```

- 思路

1. 状态表示f[i]
   1. 集合：**所有以第i个数结尾的上升子序列**
   2. 属性：Max

2. 状态计算 —— 集合划分

<img src="image\image-20231104142210545.png" alt="image-20231104142210545" style="zoom:50%;" />

以第i-1个数（倒数第2个数）是谁来进行分类：

> 0：没有第i-1这个数，即第i个数是整个序列中的第1个数
>
> 1：倒数第2个数是a[1]
>
> 2：倒数第2个数是a[2]
>
> …
>
> i-1：倒数第2个数是a[i-1]
>
> 当然，这里面的每一个数都不一定存在，因为当$a_j\ge a[i](0\le j\le i-1)$时，不符合上升子序列的要求，所以$a_j$不存在。

所以说，$f[i] = Max(f[i], f[j] + 1)\:(0\le j\le i-1)$ 当且仅当$a[j]$存在。

- 时间复杂度分析

**$状态数量O(n) \times 转移计算量O(n) = O(n^2)$**

- 代码

```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n;
int a[N], f[N];

int main()
{
	cin >> n;
	for (int i = 1; i <= n; i ++ ) cin >> a[i];

	for (int i = 1; i <= n; i ++ )
	{
		f[i] = 1; // 初始化f[0~n]=1，即只有a[i]一个数
		for (int j = 1; j < i; j ++ )
			if (a[j] < a[i])
				f[i] = max(f[i], f[j] + 1);
	}

	int res = 0;
	for (int i = 1; i <= n; i ++ ) res = max(res, f[i]);

	cout << res << endl;

	return 0;
}
```

- **怎么把最长上升子序列保存下来呢？**

用一个数组g存放每一个转移就行了。

```c++
#include <iostream>

using namespace std;

const int N = 1010;

int n;
int a[N], f[N], g[N]; // g：保存每个转移

int main()
{
	cin >> n;
	for (int i = 1; i <= n; i ++ ) cin >> a[i];

	for (int i = 1; i <= n; i ++ )
	{
		f[i] = 1; // 初始化f[0~n]=1，即只有a[i]一个数
		g[i] = 0;
		for (int j = 1; j < i; j ++ )
			if (a[j] < a[i])
				if (f[i] < f[j] + 1) // 发生状态转移
				{
					f[i] = f[j] + 1; // 更新状态
					g[i] = j; // 记录一下f[i]是从哪个状态转移过来的
				}
	}

	int k = 1;
	for (int i = 1; i <= n; i ++ )
		if (f[k] < f[i])
			k = i; // 找到最长的那个子序列

	cout << f[k] << endl;
	// 倒着输出最长上升子序列
	for (int i = 0, len = f[k]; i < len; i ++ )
	{
		cout << a[k] << ' ';
		k = g[k];
	}

	return 0;
}
```

****

### 3. 最长上升子序列 II

![image-20231104144111181](image\image-20231104144111181.png)

```
输入
7
3 1 2 1 8 5 6
输出
4
```

- **如何优化？**

看这段序列：3 1 2 1 8 5 6

3和1的最长上升子序列都是1（因为1 < 3），如果后面有一个数可以接到3后面的那么他一定可以接到1后面，由于1比3小，所以1的适用性更广，因此就没必要存3了，只存个1就行了。以此类推，按每个最长上升子序列的长度进行分类，最长上升子序列长度是1的序列只存最靠后的那个数（也就是数值最小的那个数），最长上升子序列长度是2的序列也是只存最靠后的那个数（也就是数值最小的那个数），……

因此，可以把这些最靠后且数值最小的那些数存到一个数组中。

不难发现这个数组一定是**严格单调递增**的。<img src="image\image-20231104212318272.png" alt="image-20231104212318272" style="zoom:50%;" />

- 如果我想把$a[i]$接到这个数组后面的话，要怎么接？

利用**二分**找到数组中比$a[i]$小并且最大的$q_j$，并且此时以a[i]结尾的最长上升子序列的长度就是数组中$q_j$的最长上升子序列 +1，并且$q_i\ge a[i]$，最后更新一下数组中的最长上升子序列$q_i$。

- 时间复杂度：==$O(n\log n)$==
- 代码

```c++
#include <iostream>

using namespace std;

const int N = 100010;

int n;
int a[N];
int q[N]; // 存储所有不同长度的上升子序列的结尾的最小值

int main()
{
	cin >> n;
	for (int i = 0; i < n; i ++ ) cin >> a[i];

	int len = 0; // 存储当前的最大长度
	q[0] = -2e9; // 边界设为负无穷，哨兵
	for (int i = 0; i < n; i ++ )
	{
		int l = 0, r = len;
		while (l < r)
		{
			int mid = l + r + 1>> 1;
			if (q[mid] < a[i]) l = mid;
			else r = mid - 1;
		}
		len = max(len, r + 1); // 更新len
		q[r + 1] = a[i];
	}

	cout << len << endl;

	return 0;
}
```

****

### 4. 最长公共子序列

![image-20231104144259447](image\image-20231104144259447.png)

```
输入
4 5
acbd
abedc
输出
3
```

- 思路

1. 状态表示f[i,j]
   1. 集合：**所有在第一个序列的前i个字母中出现，且在第二个序列的前j个字母中出现的子序列**
   2. 属性：最大值

2. 状态计算 —— 集合划分【难点】

<img src="image\image-20231104173523642.png" alt="image-20231104173523642" style="zoom:70%;" />

划分成四类：

1. 不选a[i],不选b[j]；$f[i-1,j-1]$
2. 不选a[i],选b[j]；$f[i-1,j]$不一定包含$b[j]$，但是我们要求的是包含$b[j]$的情况。==但是$f[i-1,j]$是严格包含`不选a[i],选b[j]`这种情况的，又因为$f[i-1,j]$是包含于$f[i,j]$当中的，所以可以用`f[i-1,j]`来替换掉`不选a[i],选b[j]`这一类。==【难点】
3. 选a[i],不选b[j]；同理，可以用`f[i,j-1]`来替换掉`选a[i],不选b[j]`。
4. 选a[i],选b[j]。$f[i-1,j-1]+1$**当且仅当$a[i] == b[j]$**。

**这样的话，这四类其实相互之间是有重叠（重复）的，但是无所谓，我们求的是最大值而不是数量。所以可以重复。**

又因为$f[i-1,j-1]$这一类中的所有情况是包含在$f[i,j-1]和f[i-1,j]$这两类其中任一类里面的。所以一般情况下，这道题的状态转移方程为：

$f[i][j] = Max(f[i-1][j] ,f[i][j-1])\\当a[i]==b[j]时，f[i][j]=Max(f[i][j], f[i-1][j-1]+1)$

- 代码

```c++
#include <iostream>

using namespace std;

const int N = 1010;

int n, m;
char a[N], b[N];
int f[N][N];

int main()
{
	cin >> n >> m;
	cin >> a + 1 >> b + 1; // 下标从1开始读入

	for (int i = 1; i <= n; i ++ )
		for (int j = 1; j <= m; j ++ )
		{
			f[i][j] = max(f[i - 1][j], f[i][j - 1]);
			if (a[i] == b[j]) f[i][j] = max(f[i][j], f[i - 1][j - 1] + 1);
		}

	cout << f[n][m] << endl;

	return 0;
}
```

****

### 5. 最短编辑距离

![image-20231104213202099](image\image-20231104213202099.png)

![image-20231104214836819](image\image-20231104214836819.png)

```
输入
10
AGTCTGACGC
11
AGTAAGTAGGC
输出
4
```

- 思路

1. 状态表示f[i,j]
   1. 集合：**所有将a[1\~i]变成b[1\~j]的操作方式**
   2. 属性：Min

2. 状态计算 —— 集合划分

以最后一步的操作进行分类：

①删：如果把a[i]删掉之后a[1\~i]和b[1\~j]相同，此时状态转移为 $f[i-1, j] + 1$

②增：在a[i]后面添加一个字符`b[j]`后的新字符串a与b相同，此时状态转移方程为 $f[i,j-1]+1$

③改：如果$a[i]\ne b[j]$才需要修改，把a[i]改为b[j]使得改完后的字符串a和b相同，此时状态转移方程为 $f[i-1,j-1]+1$

所以，最终的状态转移方程为：$f[i,j]=min(\{f[i-1,j],f[i,j-1],f[i-1,j-1]\}) + 1$

- 时间复杂度分析

$状态数量O(n^2) \times 转移计算量O(3) = O(n^2)$

- 代码

```c++
#include <iostream>

using namespace std;

const int N = 1010;

int n, m;
char a[N], b[N];
int f[N][N];

int main()
{
	cin >> n >> a + 1 >> m >> b + 1;

	// 初始化f
	for (int i = 0; i <= m; i ++ ) f[0][i] = i;
	for (int i = 0; i <= n; i ++ ) f[i][0] = i;

	for (int i = 1; i <= n; i ++ )
		for (int j = 1; j <= m; j ++ )
		{
			f[i][j] = min(f[i - 1][j] + 1, f[i][j - 1] + 1);
			if (a[i] == b[j]) f[i][j] = min(f[i][j], f[i - 1][j - 1]);
			else f[i][j] = min(f[i][j], f[i - 1][j - 1] + 1);
		}

	cout << f[n][m] << endl;

	return 0;
}
```

****

### 6. 编辑距离

![image-20231104182605908](image\image-20231104182605908.png)

```
输入
3 2
abc
acd
bcd
ab 1
acbd 2
输出
1
3
```

- 思路：与“最短编辑距离”一样，是“最短编辑距离”的一个应用题。

- 代码

```c++
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 15, M = 1010;

int n, m;
int f[N][N];
char str[M][N];

int edit_distance(char a[], char b[])
{
	int la = strlen(a + 1), lb = strlen(b + 1);

	for (int i = 0; i <= lb; i ++ ) f[0][i] = i;
	for (int i = 0; i <= la; i ++ ) f[i][0] = i;

	for (int i = 1; i <= la; i ++ )
		for (int j = 1; j <= lb; j ++ )
		{
			f[i][j] = min(f[i - 1][j], f[i][j - 1]) + 1;
			f[i][j] = min(f[i][j], f[i - 1][j - 1] + (a[i] != b[j]));
		}

	return f[la][lb];
}

int main()
{
	cin >> n >> m;
	for (int i = 0; i < n; i ++ ) cin >> str[i] + 1;

	while (m -- )
	{
		char s[N];
		int limit;
		cin >> s + 1 >> limit;

		int res = 0;
		for (int i = 0; i < n; i ++ )
			if (edit_distance(str[i], s) <= limit)
				res ++ ;

		cout << res << '\n';
	}

	return 0;
}
```



****

## 区间DP

### 1. [石子合并](https://www.acwing.com/problem/content/284/)

- 所谓区间合并就是在定义状态的时候定义的是一个区间。

![image-20231104183152766](image\image-20231104183152766.png)

![image-20231104183726951](image\image-20231104183726951.png)

```
输入
4
1 3 5 2
输出
22
```

- 思路

1. 状态表示$f[i,j]：表示从i到j这一段区间$
   1. 集合：所有将第i堆石子到第j堆石子合并成一堆石子的合并方式
   2. 属性：Min（所有这些合并方式里代价的最小值）

2. 状态计算 —— 集合划分

**以最后一次合并的分界线来分类：**
$$
假设k=j-i+1，即k为区间长度\\
那么可以分为这几类：\\
1,2,3,\cdots,k-2,k-1\\
其中1表示左边1个右边k-1个，2表示左边2个右边k-2个，\cdots，k-1表示左边k-1个右边1个。\\
$$
如果我们是将这两堆：$[i,k]、[k+1,j]$合并的话，状态转移方程就是：$f[i,k]+f[k+1,j]+s[j]-s[i-1]$，其中$s$为前缀和数组。

最终的状态转移方程是 $f[i,j]=Min\{f[i,k]+f[k+1,j]+s[j]-s[i-1]\}\quad(k=i\sim j-1)$

- **时间复杂度分析** 

$状态数量O(n^2)\times 转移计算量O(n) = O(n^3) = 300^3 = 2.7\times10^7。在1s之内可以算出来，可以AC。 $

- **枚举顺序：区间长度从小到大**

- 代码

```c++
#include <iostream>

using namespace std;

const int N = 310;

int n;
int s[N];
int f[N][N];

int main()
{
	cin >> n;
	for (int i = 1; i <= n; i ++ ) cin >> s[i];
	// 求前缀和
	for (int i = 1; i <= n; i ++ ) s[i] += s[i - 1];

	// 枚举所有状态
	for (int len = 2; len <= n; len ++ ) // 枚举区间长度（如果区间长度是1的话，合并不需要代价（即代价为0），所以区间长度从2开始枚举）
		for (int i = 1; i + len - 1 <= n; i ++ ) // 枚举所有起点
		{
			int l = i, r = i + len - 1;
			f[l][r] = 1e8; // 计算之前要先初始化成一个比较大的数，要不然每次都是0
			for (int k = l; k < r; k ++ ) // 枚举所有边界点
				f[l][r] = min(f[l][r], f[l][k] + f[k + 1][r] + s[r] - s[l - 1]);
		}

	cout << f[1][n] << endl;

	return 0;
}
```
