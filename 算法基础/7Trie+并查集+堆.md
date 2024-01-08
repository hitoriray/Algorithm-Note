## Trie树

+ **高效地**==存储==和==查找==字符串集合的一种数据结构。
+ Trie树的前提是分支节点有穷。例如：都是小写字母或大写字母的字符串，分支最多26个；都是数字，分支最多9个；等

+ Trie树的存储：

![image-20231018114346661](image\image-20231018114346661.png)

+ Trie树的查找：

1. 查找`aced`，发现存在这么一条路径并且`d`上有标记（说明存在以d结尾的这么一个单词）。返回true
2. 查找`abcf`，在找到c时发现不存在下一个节点`f`。返回false。
3. 查找`abcd`，发现存在这么一条路径但是`d`上没有标记。返回false。

### Trie字符串统计

![image-20231018114837381](image\image-20231018114837381.png)

![image-20231018114903477](image\image-20231018114903477.png)

+ 代码

```C++
#include <iostream>

using namespace std;

const int N = 100010;

 // son：存放Trie树，因为每个节点最多只能连26条边对应26个单词
 // cnt：存的是以当前节点为结尾的单词有多少个；idx：当前用到了哪个下标
int son[N][26], cnt[N], idx; // 下标是0的点，既是根节点，又是空节点
char str[N];

// 插入
void insert(char str[])
{
    int p = 0;
    for(int i = 0; str[i]; i++)
    {
        int u = str[i] - 'a'; // 当前字母对应的编号
        if(!son[p][u]) son[p][u] = ++idx; // 如果不存在这个字母的节点，则创建
        p = son[p][u]; // 更新p为它的子节点
    }
    
    cnt[p]++; // 表示以这个字母结尾的单词数量+1
}

// 查询
void query(char str[])
{
    int p = 0;
    for(int i = 0; str[i]; i++)
    {
        int u = str[i] - 'a';
        if(!son[p][u]) return 0;
        p = son[p][u];
    }
    
    return cnt[p]; // 返回以p结尾的单词数量
}

int main()
{
    int n;
    scanf("%d", &n);
    while (n--)
    {
        char op[2];
        scanf("%s%s", op, str);
        if(op[0] == 'I') insert(str);
        else printf("%d\n", query(str));
    }
    
    return 0;
}
```

### 最大异或对

![image-20231018123456212](image\image-20231018123456212.png)

```C++
int res = 0;
// 暴力枚举
for (int i = 0; i < n; i ++ ) // 暴力枚举第一个数
    for (int j = 0; j < i; j ++ )  // 暴力枚举第二个数
        res = max(res, a[i] ^ a[j]);
```

+ 代码

```C++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 100010, M = 31 * N;

int n;
int a[N];
int son[M][2], idx;

void insert(int x)
{
    int p = 0;
    for (int i = 30; i >= 0; i--)
    {
        int u = x >> i & 1;
        if (!son[p][u]) son[p][u] = ++ idx;
        p = son[p][u];
    }
}

int query(int x)
{
    int p = 0, res = 0;
    for (int i = 30; i >= 0; i -- )
    {
        int u = x >> i & 1;
        if (son[p][!u])
        {
            p = son[p][!u];
            res = res * 2 + !u;
        }
		else
        {
            p = son[p][u];
    		res = res * 2 + u;
        }
	}
    return res;
}

int main()
{
	scanf("%d", &n);
    for (int i = 0; i < n; i ++ ) scanf("%d", &a[i]);
    
    int res = 0;
    
    for (int i = 0; i < n; i ++ )
    {
        insert(a[i]); // 先插入再查询
        int t = query(a[i]);
        res = max(res, a[i] ^ t);
    }
    cout << res << endl;
    
    return 0;
}
```

![image-20231019110055879](image\image-20231019110055879.png)

![image-20231019110451531](image\image-20231019110451531.png)

****

## 并查集

+ 不管是在==面试==还是在==竞赛==中，都是非常经常出现的题目。因为它的代码非常短，且思路精巧。考验大家的思维。

+ **题目涉及到==合并==的时候，可以考虑使用并查集**

### 1. 合并集合

![image-20231018133805778](image\image-20231018133805778.png)

![image-20231018135712486](image\image-20231018135712486.png)

+ 并查集常用的两个操作：

1. 将两个集合合并
2. 询问两个元素是否在同一个集合当中

并查集可以在近乎O(1)的时间内完成这两个操作。

+ 基本原理：每个集合用一棵树来表示。树根的编号就是整个集合的编号。每个节点存储它的父节点，p[x]表示x的父节点

+ 问题1：如何判断树根？**if(p[x] == x)**
+ 问题2：如何求x的集合编号：**while(p[x] != x) x = p[x];** 最后x就是树根（集合）编号。最耗时的操作。
+ 问题3：如何合并2个集合：假设px是x的集合编号，py是y的集合编号。**p[x] = y;**
+ 优化：==路径压缩: while(p[x] != x) p[x] = find(p[x]);==

+ 代码

```C++
#include <iostream>

using namespace std;

const int N = 100010;

int n, m;		
int p[N];

// 核心操作：返回x所在集合编号 + 路径压缩
int find(int x)
{
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

int main()
{
    cin >> n >> m;
    
    // 初始化
    for(int i = 1; i <= n; i++) p[i] = i;
	
	while (m--)
    {
        char op[2]; // 用scanf读字符串的话会自动过滤掉空格和回车，但是读字符就不会，所以建议用scanf读字符串来获取字符。
        int a, b;
        scanf("%s%d%d", op, &a, &b);
        
        if (op[0] == 'M') p[find(a)] = find(b);
        else
        {
            if (find(a) == find(b)) puts("Yes");
            else puts("No");
        }
    }
    
	return 0;
}
```

****

### 2. 连通块中点的数量

![image-20231018145013787](image\image-20231018145013787.png)

![image-20231018145754924](image\image-20231018145754924.png)

**在并查集的基础上维护每个集合的size**

+ 代码

```C++
#include <iostream>

using namespace std;

const int N = 1e5 + 10;

int n, m;
int p[N], size[N]; // 只保证根节点的size是有意义的

int find(int x)
{
	if(p[x] != x) p[x] = find(p[x]);
	return p[x];
}

int main()
{
	cin >> n >> m;

	for(int i = 1; i <= n; i++)
	{
		p[i] = i;
		size[i] = 1;	
	}

	while (m --)
	{
		string op;
		int a, b;
		cin >> op;
		if(op[0] == 'C')
		{
			cin >> a >> b;
			if(find(a) == find(b)) continue; // 注意：如果a和b已经在同一个集合中，后边的size更新操作就不执行
			p[find(a)] = find(b); // 此时b为a的根节点
			size[find(b)] += size[find(a)]; // 只维护根节点b的size
		}
		else if(op[1] == '1')
		{
			cin >> a >> b;
			if(find(a) == find(b)) puts("Yes");
			else puts("No");
		}
		else
		{
			cin >> a;
			cout << size[find(a)] << '\n';
		}
	}

	return 0;
}
```

****

### 3. 网络分析

- [题目](https://www.acwing.com/problem/content/2071/)



****

### 4. 食物链

![image-20231018151510779](image\image-20231018151510779.png)

![image-20231019113036232](image\image-20231019113036232.png)

+ 输入

```
100 7
1 101 1
2 1 2
2 2 3
2 3 3
1 1 3
2 3 1
1 5 5
```

+ 输出

```
3
```

- ==维护的是每个点到根节点的距离==

+ 代码

```C++
// 把所有有关系的点都存入并查集中
// 维护每个点与根节点之间的关系
// 用每个点到根节点的距离表示它们之间的关系
// 如果某一个点到根节点的距离为1(%3 == 1)：表示它可以吃根节点，且被1吃
// 如果某一个点到根节点的距离为2(%3 == 2)：表示它可以吃1，且被根节点吃
// 如果某一个点到根节点的距离为3(%3 == 0)：表示它与根节点是同类，可以吃2

#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 50010;

int n, m;
int p[N], d[N];

int find(int x)
{
    if (p[x] != x) // 如果父节点不是根节点
    {
        int t = find(p[x]);
        d[x] += d[p[x]]; // 更新x到根节点的距离为x到根节点的距离
        p[x] = find(p[x])/*t*/; // 让x的父指针指向它父节点的根节点
    }
    return p[x];
}

int main()
{
    scanf("%d%d", &n, &m);
    
    for (int i = 1; i <= n; i ++ ) p[i] = i;
    
    int res = 0;
    while (m -- )
    {
        int t, x, y;
        scanf("%d%d%d", &t, &x, &y);
        
        if (x > n || y > n) res ++ ;
        else
        {
            int px = find(x), py = find(y);
            if (t == 1)
            {
                 // 如果x和y在一个并查集中，并且x和y不是同类，那么就是假话
                if (px == py && (d[x] - d[y]) % 3) res ++ ;
                // x和y是同类
                else if (px != py)
                {
                    p[px] = py; // 让x的根节点指向y的节点
                    // (d[x] + ? - d[y]) % 3 == 0  --> ? = dy - dx
                    d[px] = d[y] - d[x];
                }
            }
            else
            {
                // 如果x和y在同一个并查集中，那么d[x] % 3 应该比d[y] % 3 多1，即(d[x] - d[y] - 1) % 3 应该 == 0 才表示x吃y。
                if (px == py && (d[x] - d[y] - 1) % 3) res ++ ;
                // x吃y：(d[x] + ? - d[y] - 1) % 3 == 0
                else if (px != py)
                {
                    p[px] = p[y];
                    d[px] = d[y] + 1 - d[x];
                }
            }
        }
    }
    
    printf("%d", res);
    
    return 0;
}
```

****

### 5. 单链表式的并查集

- [题目](https://www.acwing.com/problem/content/1244/)
- 思路

`p[i]`: 单链表中的下一个节点。

`i`所在树的根节点：表示从`i`开始向右找，第一个没有被用过的位置。

<img src="image\image-20231111221007397.png" alt="image-20231111221007397" style="zoom:80%;" />

- 代码

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 1100010; // 最多是110万以内

int n;
int p[N];

int find(int x)
{
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

int main()
{
    cin >> n;
    for (int i = 1; i < N; i ++ ) p[i] = i; // 初始化并查集
    for (int i = 1; i <= n; i ++ )
    {
        int x;
        cin >> x;
        x = find(x);
        cout << x << ' ';
        p[x] = x + 1;
    }
    
    return 0;
}
```

****

### 6. [最小可用编号](http://120.78.128.11/Contest.jsp?cid=1158#P15)

- 代码

```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1e6 + 10;

int n;
int p[N];

int find(int x)
{
	if (p[x] != x) p[x] = find(p[x]);
	return p[x];
}

int main()
{
	cin >> n;
	for (int i = 0; i < N; i ++ ) p[i] = i;
	int left = 0;
	for (int i = 0; i < n; i ++ )
	{
		int x;
		scanf("%d", &x);
		if (x >= N) // 特判
		{
			printf("%d\n", left);
			continue;
		}
		p[x] = find(x + 1);
		if (x == left) left = p[x];
		printf("%d\n", left);
	}
	
	return 0;
}
```

****

### 7. [蝈蝈的等值关系](http://120.78.128.11/Contest.jsp?cid=1158#P9)

- 代码

```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 100010;

int n, m;
int p[N], q[N];

int find(int x)
{
	if (p[x] != x) p[x] = find(p[x]);
	return p[x];
}

int main()
{
	cin >> n >> m;
	bool flag = true;
	
	while (m -- )
	{
		int op, a, b;
		cin >> op >> a >> b;
		if (op)
		{
			int x = find(a), y = find(b);
			if (x == y) flag = false;
			p[x] = y;
		}
		else
		{
			int x = find(a), y = find(b);
			if (x == y) flag = false;
			q[x] = y;
		}
	}
	
	if (!flag) cout << "NO" << endl;
	else cout << "YES" << endl;
	
	return 0;
}
```

### 其他题目

- 例题1：http://120.78.128.11/Contest.jsp?cid=1158#P9
- 例题2：http://120.78.128.11/Contest.jsp?cid=1158#P25
- 例题3：http://120.78.128.11/Contest.jsp?cid=1157#P10

****

## 堆

+ STL中的堆`priority_queue`==不支持==修改和删除任意元素的操作

+ 下面实现的堆是可以==支持==**修改和删除任意元素的操作**。
+ 会运用到后面的Dijakstra算法中。
+ **堆是一棵完全二叉树**

+ 如何手写一个堆？

1. 插入一个数：`heap[++size] = x; up(size);`
2. 求集合当中的最小值：`heap[1];`
3. 删除最小值：`heap[1] = heap[size]; size--; down(1);`
4. ==删除任意一个元素（STL所不具备的）==：`heap[k] = heap[size]; size--; down(k); up(k); // down和up只会执行其中一个`
5. ==修改任意一个元素（STL所不具备的）==：`heap[k] = x; down(k); up(k); // down和up只会执行其中一个`

+ `down(x)`：下沉`x`下标的元素
+ `up(x)`：上浮`x`下标的元素

+ `x`的左孩子：`2x`
+ `x`的右孩子：`2x+1`

****

### 1. 堆排序

![image-20231018185039019](image\image-20231018185039019.png)

+ 代码

```C++
#include <iostream>

using namespace std;

const int N = 100010;

int n, m;
int h[N], size;

// 核心代码：下沉下标u的节点
void down(int u)
{
    int t = u; // t最后会指向当前节点和它的两个子节点中的最小元素
    if (u * 2 <= size && h[u * 2] < h[t]) t = u * 2;
    if (u * 2 + 1 <= size && h[u * 2 + 1] < h[t]) t = u * 2 + 1;
    if (u != t) // 如果最小值不是当前节点，那么就交换使得当前节点变得更小，并且递归处理。
    {
        swap(h[u], h[t]);
        down(t);
    }
}

// 核心代码：上浮下标u的节点
void up(int u)
{
    while (u / 2 && h[u / 2] > h[u])
    {
        swap(h[u / 2], h[u]);
        u /= 2;
    }
}

int main()
{
    cin >> n >> m;
    
    // 初始化
    for (int i = 1; i <= n; i++) cin >> h[i];
    size = n;
    
    for (int i = n / 2; i; i--) down(i); // 对从倒二开始向上的每一层中的每一个节点执行一次down。时间复杂度：O(n)【证明如下图】
    
    while (m -- )
    {
        cout << h[1] << ' '; // 每次输出最小值
        h[1] = h[size]; // 并且删除该最小值，用最后一个元素替换
        size -- ; // 更新size
        down(1); // 下沉替换过来的那个较大元素
    }
    
    return 0;
}
```

![image-20231018183808854](image\image-20231018183808854.png)

****

### 2. 模拟堆

![image-20231018185107328](image\image-20231018185107328.png)	

![image-20231018185119798](image\image-20231018185119798.png)

**带映射版本的堆比较少用，只在==Dijkstra算法==中会用到。**

**考试中不需要自己手写，因为有priority_queue，但是在面试的时候面试官可能会让你手写。所以练一下还是有帮助的。**

+ 代码

```C++
#include <iostream>
#include <algorithm>
#include <string>

using namespace std;

const int N = 100010;

// ph[k]: 第k个插入的点在堆里面的下标
// hp[k]: 堆里面的第k个点是第几个插入的点
// ph和hp互为反函数。
int h[N], ph[N], hp[N], size;
// p: pointer, h: heap. ph means pointer -> heap, hp means heap -> pointer

// 带映射版本的堆特有的交换
void heap_swap(int a, int b)
{
	swap(ph[hp[a]], ph[hp[b]]);
	swap(hp[a], hp[b]);
	swap(h[a], h[b]);
}

void down(int u)
{
	int t = u;
	if (u * 2 <= size && h[u * 2] < h[t]) t = u * 2;
	if (u * 2 <= size && h[u * 2 + 1] < h[t]) t = u * 2 + 1;
	if (t != u)
	{
		heap_swap(u, t); // 换成堆里特有的swap操作
		down(t);
	}
}

void up(int u)
{
	while (u / 2 && h[u / 2] > h[u])
	{
		heap_swap(u / 2, u); // 换成堆里特有的swap操作
		u /= 2;
	}
}

int main()
{
	int n, m = 0;
	scanf("%d", &n);
	while (n -- )
	{
		char op[10];
		int x, k;
		
		scanf("%s", op);
		if (!strcmp(op, "I")) // 插入一个数x
		{
			scanf("%d", &x);
			size ++ ;
			m ++ ;
			ph[m] = size; // 第m个插入的数在堆里面的size这个位置
			hp[size] = m; // 因为hp是ph的反函数，所以反过来
			h[size] = x;
			up(size);
		}
		else if (!strcmp(op, "PM")) printf("%d\n", h[1]); // 输出当前集合中的最小值
		else if (!strcmp(op, "DM")) // 删除当前集合中的最小值（当最小值不唯一时，删除最早插入的最小值）
		{
			heap_swap(1, size);
			size -- ;
			down(1);
		}
		else if(!strcmp(op, "D")) // 删除第k个插入的数
		{
			scanf("%d", &k);
			k = ph[k]; // 获取第k个插入的元素在堆中的下标
			heap_swap(k, size);
			size -- ;
			down(k), up(k);
		}
		else // 修改第k个插入的数，将其变为x
		{
			scanf("%d%d", &k, &x);
			k = ph[k]; // 获取第k个插入的元素在堆中的下标
			h[k] = x;
			down(k), up(k);
		}
	}
	
	return 0;
}
```

