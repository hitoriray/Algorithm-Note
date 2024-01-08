## 链表与邻接表

+ **动态链表**：用指针和结构体实现链表（==面试题出现较多，笔试题中一般用不到==）

```C++
struct Node
{
    int data;
    Node* next;
};
new Node(); // 非常慢
```

+ **静态链表**：用数组模拟链表（**效率更高，**==笔试题中常见==）

### 用数组模拟单链表

+ 在笔试题中用的最多（最大的用途）的是==邻接表==
+ 邻接表最大的用途：存储==树==和==图==
+ 单链表代码模板

```C++
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 100010;

// head表示头节点的下标
// e[i]:表示节点i的值是多少
// ne[i]:表示节点i的next指针是多少
// idx:存储当前已经用到了哪个点
int head, e[N], ne[N], idx;

void init()
{
    head = -1;
    idx = 0; // 表示当前可以从0号点开始用
}

// 将x插到头节点
void add_to_head(int x) // 在算法题中用的最多
{
    e[idx] = x; // new一个节点
    ne[idx] = head; // 将x的next指向head的next
    head = idx; // 更新head的next为x的下标
    idx++; // 更新idx，使其指向还未被使用的空间
}

// 将x插到下标是k的点的后面
void add(int k, int x)
{
    e[idx] = x;
    ne[idx] = ne[k];
    ne[k] = idx;
    idx++;
}

// 将下标是k的点的后面那个点删掉
void remove(int k)
{
    ne[k] = ne[ne[k]]; // 将k的next指向k的next的next
}
```

- [题目](https://www.acwing.com/problem/content/828/)

- 代码

```C++
// 参考上述代码
int main()
{
    init();
    
    int m;
    cin >> m;
    while (m--)
    {
        char op;
        int k, x;
        
        cin >> op;
        if(op == 'H')
        {
            cin >> x;
            add_to_head(x);
        }
        else if(op == 'D')
        {
            cin >> k;
            if(!k) head = ne[head];
            else remove(k - 1);
        }
        else
        {
            cin >> k >> x;
            add(k - 1, x);
        }
    }
    
    for(int i = head; i != -1; i = ne[i]) cout << e[i] << ' ';
        
    return 0;
}
```

****

### 用数组模拟双链表

+ 可以用来优化某些问题
+ 双链表代码模板

```C++
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 100010;

int m;
int e[N], l[N], r[N], idx;

// 初始化
void init()
{
    // 0表示头节点，1表示尾节点
	r[0] = 1, l[1] = 0; // 节点0的next（右边）为节点1，节点1的pre（左边）为节点0
    idx = 2;
}

// 在下标是k的点的右边插入x
void add(int k, int x)
{	
    e[idx] = x;
    r[idx] = r[k];
    l[idx] = k;
    l[r[k]] = idx;
    r[k] = idx;
	idx++;
}
// 在下标是k的点的左边插入x，直接调用add(l[k], x)即可

// 删除第k个点
void remove(int k)
{
    r[l[k]] = r[k];
    l[r[k]] = l[k];
}
```

****

## 栈与队列

用数组模拟的栈和队列相对于STL中的`stack`和`queue`有一个**致命的好处**就是：==速度快！！！==

在开启**O2优化**后，它们之间的速度就相差无几了，但是99%的比赛方都不会开O2优化！

### 用数组模拟栈

```C++
#include <iostream>
using namespace std;
const int N = 100010;
// stk：栈，tt：指向栈顶元素
int stk[N], tt;
// 插入
void push(int x) { stk[++tt] = x; }
// 弹出
void pop() { tt--; }
// 判断是否为空
void empty() { return tt == 0; }
// 取出栈顶元素
int top() { return stk[tt]; }
```

### 用数组模拟队列

```C++
#include <iostream>
using namespace std;
const int N = 100010;
// 在队尾插入元素，队头弹出元素
int q[N], hh, tt = -1;
// 插入
void push(int x) { q[++tt] = x; }
// 弹出
void pop() { hh++; }
// 判断是否为空
void empty() { return hh > tt; }
// 取出队头元素
int top() { return q[hh]; }
```

****

### 单调栈

+ 比较抽象，但是题型少，常见的就那几种。

1. **给定一个数，求出每一个数左边（或右边）离它最近的且比他小（或大）的数在什么地方。**

+ 题目

![image-20231016215954523](image\image-20231016215954523.png)

```C++
// 暴力枚举：O(n^2)
void violence()
{
    for(int i = 0; i < n; i++)
        for(int j = i - 1; j >= 0; j--)
            if(a[i] > a[j])
            {
                cout << a[j] << endl;
                break;
            }
} 
```

+ 暴力枚举中有很多没用到的东西。
+ 以下为单调栈解法，暴力枚举的改进版。

```C++
#include <iostream>

using namespace std;

const int N = 10010;

int n;
int stk[N], tt;

int main()
{
    scanf("%d", &n);
    // 单调栈：O(n)
    for(int i = 0; i < n; i++)
    {
        int x;
        scanf("%d", &x);
        while (tt && stk[tt] >= x) tt--; // 当栈不空且栈顶元素大于x，说明再也用不到这个栈顶元素了，弹出栈顶元素即可。
        if (tt) printf("%d ", stk[tt]); // 如果栈不空，说明此时栈顶元素就是x的左边离他最近且比它小的元素
        else printf("-1 "); // 否则，说明x左边不存在离他最近且比它小的元素了，返回-1
        
        stk[++tt] = x; // 把x插到栈里面去
    }
    
    return 0;
}
```

****

### 单调队列

+ 与单调栈类似，最常用的题型就那么几种。

> 最常用的题型：求**滑动窗口**里面的==最大值==或==最小值==。

+ 题目

![image-20231016222237551](image\image-20231016222237551.png)

![image-20231016222300550](image\image-20231016222300550.png)

![image-20231016222312822](image\image-20231016222312822.png)

```C++
// 暴力枚举：用队列维护滑动窗口大小，每次枚举队列中的最大值或最小值
```

+ 考虑队列中的某些元素是否是没有用到的，删除这些元素，看一下会不会得到单调性。

![image-20231016225137909](image\image-20231016225137909.png)

+ 例如，当-3进入队列后，3和-1比-3小，所以在后边的窗口中肯定用不到，可以都删了。
+ 此时窗口中剩下的都是比-3还要小的，就构成了一个单调队列。
+ 此时，想要在单调队列中取极值就考虑两个端点即可，想要取某一个值则考虑二分。

```C++
#include <iostream>

using namespace std;

const int N = 1e6 + 10;

int n, k;
int a[N], q[N]; // 队列q中存放的是下标

int main()
{
    cin >> n >> k;
    for(int i = 0; i < n; i++) cin >> a[i];
    
    int hh = 0, tt = -1;
    // 滑动窗口最大值
    for(int i = 0; i < n; i++)
    {
        // 判断队头是否已经滑出窗口
        if (hh <= tt && i - k + 1 > q[hh]) hh++; // 如果队列不空且队头元素滑出窗口，则将队头元素出队
        while (hh <= tt && a[q[tt]] <= a[i]) tt--; // 如果队列不空且队尾元素小于等于当前元素，则将队尾元素出队
        q[++tt] = i; // 将当前元素插入队列中
        if(i >= k - 1) cout << a[q[hh]] << ' '; // 本题中只有当数组长度到达滑动窗口的长度才开始输出
    }
    puts("");
    
    // 滑动窗口最小值
    for(int i = 0; i < n; i++)
    {
        // 判断队头是否已经滑出窗口
        if (hh <= tt && i - k + 1 > q[hh]) hh++; // 如果队列不空且队头元素滑出窗口，则将队头元素出队
        while (hh <= tt && a[q[tt]] >= a[i]) tt--; // 如果队列不空且队尾元素大于等于当前元素，则将队尾元素出队
        q[++tt] = i; // 将当前元素插入队列中
        if(i >= k - 1) cout << a[q[hh]] << ' '; // 本题中只有当数组长度到达滑动窗口的长度才开始输出
    }
    puts("");
    
    return 0;
}
```

****

## KMP

- **详细笔记参考**：https://blog.csdn.net/m0_75186429/article/details/134505128?spm=1001.2014.3001.5501
+ 题目

![image-20231017091459675](image\image-20231017091459675.png)

![image-20231017091516291](image\image-20231017091516291.png)

1. 先想一下暴力算法怎么做？

```C++
char s[N], p[M]; // s表示原串，p表示模板串
for (int i = 1; i <= n; i++)
{
    bool flag = true;
    if (i + m > n) return false; // 匹配不到了，直接返回false
    for (int j = 1; j <= m; j++)
		if (s[i + j - 1] != p[j])
        {
            flag = false;
            break;
        }
}
```

<img src="image\image-20231120130227752.png" alt="image-20231120130227752" style="zoom:67%;" />

2. 怎么优化？ 

`next[i]`：以`i`为结尾的后缀最多有多少个字符与前缀相等。

+ KMP匹配的过程

![image-20231017093948423](image\image-20231017093948423.png)

+ 求`next`数组的过程

![image-20231017095306923](image\image-20231017095306923.png)

+ 代码

```C++
#include <iostream>
#include <cstring>

using namespace std;

const int N = 10010, M = 100010;

int n, m;
char p[N], s[M]; // p：模板串，s：原串
int ne[N]; // next数组

int main()
{
    scanf("%d%s%d%s", &n, p + 1, &m, s + 1); // 下标从1开始输入p和s
    // cin >> n >> p + 1 >> m >> s + 1;
    
    // 求next数组
    for (int i = 2, j = 0; i <= n; i++)
    { // ne[1] = 0，因为如果第一个匹配不上，就只能从0开始重新匹配了
        while (j && p[i] != p[j + 1]) j = ne[j]; // 与KMP匹配过程类似
        if (p[i] == p[j + 1]) j++;
        ne[i] = j; // 记录ne[i]的值为j
    }
    
    // KMP匹配过程
    for (int i = 1, j = 0; i <= m; i++)
    {
        while (j && s[i] != p[j + 1]) j = ne[j]; // 如果s[i]与p[j + 1]不能匹配，将j移动到ne[j]的位置继续与s[i]匹配，直到j为0
        if (s[i] == p[j + 1]) j++; // 匹配成功，j向后移动
        if (j == n) // 此时，整个字符串都匹配成功
        {
            cout << i - n << ' '; // 输出匹配位置（下标从0开始）
            j = ne[j]; // 从ne[j]开始，继续下一次匹配
        }
    }
    
    return 0;
}
```

