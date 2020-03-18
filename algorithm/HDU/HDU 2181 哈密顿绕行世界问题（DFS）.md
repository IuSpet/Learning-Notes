# HDU 2181 哈密顿绕行世界问题（DFS）

### 题意

​		由20个顶点的图，输入每个点联通的点，输入出发点后，按字典序输出所有通过所有点回到出发点的路线

### 思路

​		将每个顶点联通的点从小到大存储后DFS找出所有路线

### 代码

```cpp
/*
	HDU 2181
	dfs
*/
#include<iostream>
#include<vector>
#include<algorithm>
#include<cstring>
using namespace std;
vector<int> ans;
vector<int> g[25];
bool vis[25];
int kase = 1;
int x, m;
void dfs(int k)
{
	for (int i = 0; i < 3; i++)
	{
        //回到出发点但没有遍历所有点
		if (g[k][i] == m && ans.size() != 20) continue;
		if (!vis[g[k][i]]) {
			vis[g[k][i]] = true;
			ans.push_back(g[k][i]);
            //遍历所有点，两遍出发点
			if (ans.size() == 21)
			{
				cout << kase++ << ": ";
				for (auto& v : ans) cout<< " " << v;
				cout << endl;
				vis[g[k][i]] = false;
				ans.pop_back();
				return;
			}
			dfs(g[k][i]);
			vis[g[k][i]] = false;
			ans.pop_back();
		}
	}
}
int main()
{
	cin.sync_with_stdio(false);
	memset(vis, 0, sizeof(vis));
	for (int i = 1; i <= 20; i++)
	{
		for (int j = 0; j < 3; j++)
		{
			cin >> x;
			g[i].push_back(x);
		}
        //确保字典序
		sort(g[i].begin(), g[i].end());
	}
	while (cin >> m)
	{
		if (m == 0) return 0;
		ans.push_back(m);
		dfs(m);
	}
}

```

