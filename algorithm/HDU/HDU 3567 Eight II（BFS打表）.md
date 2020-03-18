# HDU 3567 Eight II（BFS打表）

### 题目大意

​		还是八数码问题，输入起始结束状态，输出最小字典序的操作顺序

### 思路

​		还是用BFS打表，为了使操作最短，处理出X在不同位置不断操作能达到的各种状态，至于为什么根据X的不同处理，除了X外，其他数字可以随意替换，如：

1 2 3						3 4 5

4 5 6			=>		7 8 X

7 8 X						6 2 1

可以替换成

a b c						c d e

d e f			=>		h i X

h i X						f  b a

经过这样的映射变换后，操作的序列不变

​		所以，只要将X某一位置的一种情况进行BFS得到所有状态打表，就可以将X在同一位置的起始状态映射为该状态，再用同样的映射规则处理结束状态，就可以在表中查出操作顺序

​		另外为了保证最小字典序，BFS中要按照dlru的顺序进行操作

### 代码

```cpp
/*
	HDU 3567
	BFS打表
*/
#include<iostream>
#include<vector>
#include<map>
#include<stack>
#include<queue>
#include<string>
#include<cstring>
using namespace std;
constexpr int maxn = 500000;

struct node
{
    //棋盘状态
	vector<int> grid;
    //X的位置
	int pos;
};
//阶乘
vector<int> c({ 1, 1, 2, 6, 24, 120, 720, 5040, 40320, 362880 });
//防止BFS中重复访问
bool vis[maxn];
//储存前驱状态
int pre[10][maxn];
//从前驱状态过来的操作
char step[10][maxn];
int cantor(vector<int>& permutation)
{
	int res = 0;
	for (int i = 0; i < 9; i++)
	{
		int a = 0;
		for (int j = i + 1; j < 9; j++)
		{
			if (permutation[i] > permutation[j])
				a++;
		}
		res += a * c[9 - i - 1];
	}
	return res;
}
void bfs(int k, node s)
{
	memset(vis, 0, sizeof(vis));
	memset(pre[k], -1, sizeof(pre[k]));
	queue<node> qn;
	qn.push(s);
	int head = cantor(s.grid);
	vis[head] = true;
	while (!qn.empty())
	{
		node now = qn.front(); qn.pop();
		int pre_val = cantor(now.grid);
		if (now.pos < 6)
		{
			//down
			swap(now.grid[now.pos], now.grid[now.pos + 3]);
			int val = cantor(now.grid);
			if (!vis[val])
			{
				vis[val] = true;
				pre[k][val] = pre_val;
				step[k][val] = 'd';
				now.pos += 3;
				qn.push(now);
				now.pos -= 3;
			}
			swap(now.grid[now.pos], now.grid[now.pos + 3]);
		}
		if (now.pos != 0 && now.pos != 3 && now.pos != 6)
		{
			//left
			swap(now.grid[now.pos], now.grid[now.pos - 1]);
			int val = cantor(now.grid);
			if (!vis[val])
			{
				vis[val] = true;
				pre[k][val] = pre_val;
				step[k][val] = 'l';
				now.pos -= 1;
				qn.push(now);
				now.pos += 1;
			}
			swap(now.grid[now.pos], now.grid[now.pos - 1]);
		}
		if (now.pos != 2 && now.pos != 5 && now.pos != 8)
		{
			//right
			swap(now.grid[now.pos], now.grid[now.pos + 1]);
			int val = cantor(now.grid);
			if (!vis[val])
			{
				vis[val] = true;
				pre[k][val] = pre_val;
				step[k][val] = 'r';
				now.pos += 1;
				qn.push(now);
				now.pos -= 1;
			}
			swap(now.grid[now.pos], now.grid[now.pos + 1]);
		}
		if (now.pos > 2)
		{
			//up
			swap(now.grid[now.pos], now.grid[now.pos - 3]);
			int val = cantor(now.grid);
			if (!vis[val])
			{
				vis[val] = true;
				pre[k][val] = pre_val;
				step[k][val] = 'u';
				now.pos -= 3;
				qn.push(now);
				now.pos += 3;
			}
			swap(now.grid[now.pos], now.grid[now.pos - 3]);
		}
	}
}
void ini()
{
	vector<int> tmp;
	for (int i = 0; i < 9; i++)
	{
		int t = 1;
		tmp.clear();
		for (int j = 0; j < 9; j++)
		{
			if (i == j) tmp.push_back(9);
			else
			{
				tmp.push_back(t);
				t++;
			}
		}
		bfs(i, { tmp,i });
	}
}
int main()
{
	cin.sync_with_stdio(false);
	int t;
	string str;
	vector<int> que;
	int m[10];
	ini();
	cin >> t;
	for (int kase = 1; kase <= t; kase++)
	{
		cin >> str;
		int k = 0;
		int j = 1;
		que.clear();
		for (int i = 0; i < 9; i++)
		{
			if (str[i] == 'X') k = i;
			else
			{
                //映射表
				m[str[i] - '0'] = j++;
			}
		}
		cin >> str;
		for (int i = 0; i < 9; i++)
		{
			if (str[i] == 'X') que.push_back(9);
			else
			{
                //按照映射改变状态
				que.push_back(m[str[i] - '0']);
			}
		}
		//for (auto v : que) cout << v << " ";
		//cout << endl;
		int que_val = cantor(que);
		string ans;
		while (pre[k][que_val] != -1)
		{
			int tmp = pre[k][que_val];
			ans.push_back(step[k][que_val]);
			que_val = tmp;
		}
		cout << "Case " << kase << ": " << ans.length() << endl;
		for (auto p = ans.rbegin(); p != ans.rend(); p++)
			cout << *p;
		cout << endl;
	}
}
```

