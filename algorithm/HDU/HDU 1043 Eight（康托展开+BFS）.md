# HDU 1043 Eight（康托展开+BFS）

### 题目大意

​		八数码问题，输入9个字符代表一个3*3的棋盘，对于能还原的输入，输出一段包含 u、d、l、r 操作的操作序列，不能还原的输入输出“unsolvable”

### 思路

​		输入有多组，所以考虑先预处理出所有情况的操作序列，再根据输入进行输出

​		因为要遍历所有情况，所以选择 BFS 即可

​		另外对于棋盘的所有情况，与全排列类似，用 9 代替 x 进行康托映射，压缩空间

### 代码

```cpp
/*
	HDU1043
	八数码 BFS搜索 + 康托展开
*/
#include<iostream>
#include<vector>
#include<string>
#include<map>
#include<queue>
#include<cstring>

using namespace std;
struct node
{
	int cantor_value;
	int pos;
};
//阶乘
vector<int> c({ 1, 1, 2, 6, 24, 120, 720, 5040, 40320, 362880 });
bool vis[400000];
queue<node> qn;
map<int, string> mis;
int cantor(vector<int>& permutation)
{
	int res = 0;
	for (int i = 0; i < permutation.size(); i++)
	{
		int a = 0;
		for (int j = i + 1; j < permutation.size(); j++)
		{
			if (permutation[i] > permutation[j])
				a++;
		}
		res += a * c[permutation.size() - i - 1];
	}
	return res;
}
vector<int> decantor(int num, int n)
{
	vector<int> res;
	vector<int> nums;
	for (int i = 1; i <= n; i++) nums.push_back(i);
	for (int i = n - 1; i >= 0; i--)
	{
		int a = num / c[i];
		res.push_back(nums[a]);
		nums.erase(nums.begin() + a);
		num -= a * c[i];
	}
	return res;
}
void bfs()
{
	while (!qn.empty())
	{
		node now = qn.front(); qn.pop();
		vector<int> per = decantor(now.cantor_value, 9);
		if (now.pos != 0 && now.pos != 3 && now.pos != 6)
		{
			//left
			vector<int> tmp(per);
			tmp[now.pos] = tmp[now.pos - 1];
			tmp[now.pos - 1] = 9;
			int val = cantor(tmp);
			if (!vis[val])
			{
				vis[val] = true;
				mis[val] = 'r' + mis[now.cantor_value];
				qn.push({ val, now.pos - 1 });
			}
		}
		if (now.pos > 2)
		{
			//up
			vector<int> tmp(per);
			tmp[now.pos] = tmp[now.pos - 3];
			tmp[now.pos - 3] = 9;
			int val = cantor(tmp);
			if (!vis[val])
			{
				vis[val] = true;
				mis[val] = 'd' + mis[now.cantor_value];
				qn.push({ val, now.pos - 3 });
			}
		}
		if (now.pos != 2 && now.pos != 5 && now.pos != 8)
		{
			//right
			vector<int> tmp(per);
			tmp[now.pos] = tmp[now.pos + 1];
			tmp[now.pos + 1] = 9;
			int val = cantor(tmp);
			if (!vis[val])
			{
				vis[val] = true;
				mis[val] = 'l' + mis[now.cantor_value];
				qn.push({ val, now.pos + 1 });
			}
		}
		if (now.pos < 6)
		{
			//down
			vector<int> tmp(per);
			tmp[now.pos] = tmp[now.pos + 3];
			tmp[now.pos + 3] = 9;
			int val = cantor(tmp);
			if (!vis[val])
			{
				vis[val] = true;
				mis[val] = 'u' + mis[now.cantor_value];
				qn.push({ val,now.pos + 3 });
			}
		}
	}
}
void ini()
{
	vector<int> ini;
	for (int i = 1; i <= 9; i++) ini.push_back(i);
	int ini_val = cantor(ini);
	vis[ini_val] = true;
	mis[ini_val] = "";
	qn.push({ ini_val, 8 });
	bfs();
}
int main()
{
	memset(vis, 0, sizeof(vis));
	cin.sync_with_stdio(false);
	ini();
	string input;
	vector<int> in;
	while (cin >> input)
	{
		if (input[0] == 'x') in.push_back(9);
		else in.push_back(input[0] - 48);
		if (in.size() == 9)
		{
			int val = cantor(in);
			if (vis[val])
				cout << mis[cantor(in)] << endl;
			else
				cout << "unsolvable" << endl;
			in.clear();
		}
	}
}
```

