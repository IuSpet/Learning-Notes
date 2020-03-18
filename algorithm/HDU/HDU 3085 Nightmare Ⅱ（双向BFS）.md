# HDU 3085 Nightmare Ⅱ（双向BFS）

### 题意

&emsp;&emsp;A、B在迷宫中，A每回合能走3步，B每回合能走1步，迷宫中还有2个鬼，每回合最先活动，会向两步内的格子分裂，A、B不能碰鬼，求他们最快见面的回合数

### 思路

​		题目中描述A能走3步，实际上意思是A每回合能移动[0,3]步，B也同理，所以只要考虑A、B尽可能最远到达的区域，在当前回合这些区域内都是可达的

&emsp;&emsp;将整个题目可以考虑为一个像染色的模型，A、B、鬼分别以每回合3、1、2的速度染色，染色的部分就是他们当前回合能够到达的所有部分。找出A、B最快染到对方颜色块上的回合数

​		A、B每回合都单独行动，所以考虑用双向BFS

### 代码

```cpp
/*
	HDU 3085
	双向BFS
*/
#include<iostream>
#include<queue>
#include<algorithm>
#include<cstring>
#define min(x,y) x>y?y:x
using namespace std;
const int maxn = 810;
struct node
{
	int x, y, steps;
}a1, a2;
char maze[maxn][maxn];
int t, m, n;
vector<pair<int, int>> ghosts;
int dx[4] = { 1,-1,0,0 }, dy[4] = { 0,0,1,-1 };
//和最近的一个鬼的出生点的距离
int Manhattan(int x, int y) {
	int res = 1000000;
	res = min(res, abs(ghosts[0].first - x) + abs(ghosts[0].second - y));
	res = min(res, abs(ghosts[1].first - x) + abs(ghosts[1].second - y));
	return res;
}
//行数，列数，回合数，剩余步数，自己涂色，对方涂色，队列
bool dfs(int x, int y, int s, int k, char c1, char c2, queue<node>& q)
{
	int dis = Manhattan(x, y);
	//上一回合能走到这里，但是每回合鬼先走，所以要先判断本回合鬼有没有到这里
	if (dis <= 2 * (s + 1)) return false;
	for (int i = 0; i < 4; i++)
	{
		int xx = x + dx[i];
		int yy = y + dy[i];
		if (xx < 0 || yy < 0 || xx > n || yy > m) continue;
		dis = Manhattan(xx, yy);
		//鬼能到这里，剪枝
		if (dis <= 2 * (s + 1)) continue;
		//空地
		if (maze[xx][yy] == '.')
		{
			q.push({ xx,yy,s + 1 });
            //染自己的颜色
			maze[xx][yy] = c1;
			if (k > 1) if (dfs(xx, yy, s, k - 1, c1, c2, q)) return true;
		}
		else if (maze[xx][yy] == c1)
		{
			if (k > 1) if (dfs(xx, yy, s, k - 1, c1, c2, q)) return true;
		}
		//相遇
		else if (maze[xx][yy] == c2)
		{
			return true;
		}
	}
	return false;
}
int bfs()
{
	queue<node> q1, q2;
	q1.push(a1);
	q2.push(a2);
	int s = 0;
	while (!q1.empty() || !q2.empty())
	{
		while (!q1.empty())
		{
			node ey = q1.front();
			if (ey.steps > s) break;
			else
			{
				q1.pop();
				//走三步
				if (dfs(ey.x, ey.y, ey.steps, 3, 'a', 'b', q1)) return ey.steps + 1;
			}
		}
		while (!q2.empty())
		{
			node gf = q2.front();
			if (gf.steps > s) break;
			else
			{
				q2.pop();
				if (dfs(gf.x, gf.y, gf.steps, 1, 'b', 'a', q2)) return gf.steps + 1;
			}
		}
		s++;
	}
	return -1;
}
int main()
{
	cin.sync_with_stdio(false);
	cin >> t;
	while (t--)
	{
		memset(maze, 0, sizeof(maze));
		ghosts.clear();
		cin >> n >> m;
		for (int i = 0; i < n; i++) cin >> maze[i];
		for (int i = 0; i < n; i++)
			for (int j = 0; j < m; j++)
			{
				if (maze[i][j] == 'M')
				{
					a1.x = i;
					a1.y = j;
					a1.steps = 0;
					maze[i][j] = 'a';
				}
				else if (maze[i][j] == 'G')
				{
					a2.x = i;
					a2.y = j;
					a2.steps = 0;
					maze[i][j] = 'b';
				}
				else if (maze[i][j] == 'Z')
				{
					ghosts.push_back({ i,j });
				}
			}
		int ans = bfs();
		cout << ans << endl;		
	}
}
```

