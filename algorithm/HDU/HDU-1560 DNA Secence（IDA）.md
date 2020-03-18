# HDU-1560 DNA Secence（IDA*）

### 题意

&emsp;&emsp;输入一些只包含A、C、G、T的字符串，找出一条最短的字符串，使得输入都是该字符串的子序列（不用连续），输出找到的字符串的长度

### 思路

&emsp;&emsp;考虑类似trie树这样的结构，从根开始，所有字符串沿着一条路下来能按顺序匹配上自身所有字符，该条路就是最终要找的字符串

&emsp;&emsp;所以从跟开始向下搜索，没个节点处也要保存到该节点时各个字符串的状态。

&emsp;&emsp;一开始尝试直接用BFS，但是随着层数增多，队列内的节点也越来越多，再加上每个节点入队时保存了该点字符串的信息，和预料的一样，mle了

&emsp;&emsp;所以考虑尽快处理一条路上的数据处理掉，减小空间占用，考虑使用DFS搜索，但是要控制每次递归的层数，不断加深搜索层数，即迭代加深搜索（IDA*）

### 代码

```C++
/*
	HDU 1560 
	IDA*
*/
#include<vector>
#include<iostream>
#include<string>
#include<cmath>
#include<algorithm>
#define max(x,y) x>y?x:y
using namespace std;
int k, n, ans;
vector<string> gens;
string gen;
string dna = "ACGT";
//当前层数，最大层数，各个字符串匹配到的位置
void dfs(int colum, int maxcolum, vector<int>& vi)
{
    //当前层数已经大于期望最深层数，剪枝
	if (colum > maxcolum) return;
    //当前所有字符串剩下最长的长度
	int tmp = 0;   
	for (int i = 0; i < n; i++)
	{
		tmp = max(tmp, gens[i].length() - vi[i]);
	}
	if (tmp == 0)
	{
		ans = colum;
		return;
	}
	if (colum + tmp > maxcolum) return;
    //每一层都是A、C、G、T四个字符，依次判断每个字符串能否匹配上
	for (int i = 0; i < 4; i++)
	{
		bool flag = false;
		vector<int> pos(vi);
		for (int j = 0; j < n; j++)
		{
			if (pos[j] >= gens[j].length()) continue;
			if (dna[i] == gens[j][pos[j]])
			{
				pos[j]++;
				flag = true;
			}
		}
		if (flag)
		{
			dfs(colum + 1, maxcolum, pos);			
			if (ans != 0) return;
		}
	}
}
int main()
{
	cin.sync_with_stdio(false);
	cin >> k;
	while (k--)
	{
		cin >> n;
		int depth = 0;
		ans = 0;
		gens.clear();
		for (int i = 0; i < n; i++)
		{
			cin >> gen;
			depth = max(depth, gen.length());
			gens.push_back(gen);
		}
		vector<int> pos(n, 0);
		for (;; depth++)
		{
			dfs(0, depth, pos);
			if (ans != 0) break;
		}
		cout << ans << endl;
	}
}
```