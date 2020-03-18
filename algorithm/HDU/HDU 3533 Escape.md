# HDU 3533 Escape（BFS）

### 题意

​		A从棋盘（0，0）前往（m，n）每回合消耗一点能量，地图上有一些碉堡，碉堡周期性发射子弹，子弹沿直线运动，每回合在棋盘上一点，碉堡可以拦住子弹的运动，子弹互相不影响，A在移动过程中不能和碉堡子弹处于同一点，输出最短到终点时间，无法到达输出“Bad luck!”

### 思路

​		棋盘上最短路，考虑用BFS或者A*，这题BFS足够

​		难点在于每走一到一点，要计算在当前时刻该点有无子弹。既然碉堡能够挡住子弹，那么每当A走到一点，只需考虑A东西南北方向上最近的四个碉堡。再根据这四个碉堡的朝向以及发射子弹的速度间隔计算当前时刻A所在点有无子弹

### 代码

```cpp
/*
	HDU 3533
    BFS
*/
#include<iostream>
#include<vector>
#include<algorithm>
#include<cstring>
#include<queue>
#include<cstdio>
using namespace std;
struct node {
	int v, t, r, c;
	char dir;
};
struct spy {
	int x, y, d;
};
int m, n, k, d;
vector<node> row[105], col[105];
bool vis[105][105][1005];
char dir[5];
bool cmp1(const node& a, const node& b)
{
	return a.c < b.c;
}
bool cmp2(const node& a, const node& b)
{
	return a.r < b.r;
}
bool move(int x, int y, int t)
{
	//x,y:坐标，t:时间
	if (x < 0 || y < 0 || x > m || y > n) return false;
	if (vis[x][y][t]) return false;
	//每个方向上相邻的碉堡的坐标
	int rl = -1, rr = -1, cu = -1, cd = -1;
	//row bullet
	for (int i = 0; i < row[x].size(); i++)
	{
		node& c = row[x][i];
		if (c.c < y) rl = i;
		else if (c.c == y) return false;
		else
		{
			rr = i;
			break;
		}
	}
	//西边的碉堡
	if (rl != -1)
	{
		node& c = row[x][rl];
		//判断子弹朝向
		if (c.dir == 'E')
		{
			int dis = y - c.c;
			//判断子弹能否到达
			if (!(dis % c.v))
			{
				//第一发子弹到达的时间
				int round = dis / c.v;
				//判断当前是否有子弹
				if ((t - round) % c.t == 0) return false;
			}
		}
	}
	//东边的碉堡
	if (rr != -1)
	{
		node& c = row[x][rr];
		//判断子弹朝向
		if (c.dir == 'W')
		{
			int dis = c.c - y;
			//判断子弹能否到达
			if (!(dis % c.v))
			{
				//判断当前是否有子弹
				int round = dis / c.v;
				if ((t - round) % c.t == 0) return false;
			}
		}
	}
	//cloumn bullet
	for (int i = 0; i < col[y].size(); i++)
	{
		node& c = col[y][i];
		if (c.r < x) cu = i;
		else if (c.r == x) return false;
		else
		{
			cd = i;
			break;
		}
	}

	//北边
	if (cu != -1)
	{
		node& c = col[y][cu];
		//判断子弹朝向
		if (c.dir == 'S')
		{
			int dis = x - c.r;
			//判断子弹能否到达
			if (!(dis % c.v))
			{
				//判断当前是否有子弹
				int round = dis / c.v;
				if ((t - round) % c.t == 0) return false;
			}
		}
	}
	//南边
	if (cd != -1)
	{
		node& c = col[y][cd];
		//判断子弹朝向
		if (c.dir == 'N')
		{
			int dis = c.r - x;
			//判断子弹能否到达
			if (!(dis % c.v))
			{
				//判断当前是否有子弹
				int round = dis / c.v;
				if ((t - round) % c.t == 0) return false;
			}
		}
	}
	vis[x][y][t] = true;
	return true;
}
int bfs()
{
	queue<spy> qs;
	if (move(0, 0, 0))	qs.push({ 0,0,d });
	else return -1;
	while (!qs.empty())
	{
		spy A = qs.front(); qs.pop();
		if (m + n - A.x - A.y > A.d) continue;
		if (A.x == m && A.y == n) return d - A.d;
		spy tmp = A;
		tmp.d -= 1;
		//stay
		if (move(tmp.x, tmp.y, d - tmp.d))
		{
			qs.push(tmp);
		}
		//N
		if (move(tmp.x - 1, tmp.y, d - tmp.d))
		{
			tmp.x -= 1;
			qs.push(tmp);
			tmp.x += 1;
		}
		//S
		if (move(tmp.x + 1, tmp.y, d - tmp.d))
		{
			tmp.x += 1;
			qs.push(tmp);
			tmp.x -= 1;
		}
		//W
		if (move(tmp.x, tmp.y - 1, d - tmp.d))
		{
			tmp.y -= 1;
			qs.push(tmp);
			tmp.y += 1;
		}
		//E
		if (move(tmp.x, tmp.y + 1, d - tmp.d))
		{
			tmp.y += 1;
			qs.push(tmp);
			tmp.y -= 1;
		}
	}
	return -1;
}
int main()
{
	while (~scanf("%d%d%d%d", &m, &n, &k, &d))
	{
		for (int i = 0; i < 101; i++)
		{
			row[i].clear();
			col[i].clear();
		}
		for (int i = 0; i < k; i++)
		{
			//cin >> dir;
			scanf("%s", dir);
			node tmp;
			tmp.dir = dir[0];
			//cin >> tmp.t >> tmp.v >> tmp.r >> tmp.c;
			scanf("%d%d%d%d", &tmp.t, &tmp.v, &tmp.r, &tmp.c);
			row[tmp.r].push_back(tmp);
			col[tmp.c].push_back(tmp);
		}
		for (int i = 0; i <= m; i++)
		{
			sort(row[i].begin(), row[i].end(), cmp1);
		}
		for (int i = 0; i <= n; i++)
		{
			sort(col[i].begin(), col[i].end(), cmp2);
		}
		memset(vis, 0, sizeof(vis));
		int ans = bfs();
		if (ans > 0) printf("%d\n", ans);
		else printf("Bad luck!\n");
	}
}
```

### 其他

​		debug过程中，写了一个数据产生器，可以和题解对拍

```python
import random

dirlist = ['W', 'E', 'N', 'E']
with open("input.txt", "w") as f:
    # 组数
    for kase in range(20):
        n = int(random.random() * 10) + 2
        m = int(random.random() * 10) + 2
        k = int(random.random() * (m * n)) + 1
        d = m + n + int(random.random() * k)
        f.write("%d %d %d %d\n" % (n, m, k, d))
        castle = [(0, 0), ]
        for i in range(k):
            index = int(random.random() * 4)
            dir = dirlist[index]
            t = int(random.random() * 10) + 1
            v = int(random.random() * 10) + 1
            x = int(random.random() * (n + 1))
            y = int(random.random() * (m + 1))
            while (x, y) in castle:
                x = int(random.random() * (n + 1))
                y = int(random.random() * (m + 1))
            castle.append((x, y))
            f.write("%c %d %d %d %d\n" % (dir, t, v, x, y))
        f.write("\n")

```

