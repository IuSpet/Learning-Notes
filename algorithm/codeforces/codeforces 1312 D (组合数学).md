# codeforces 1312 D (组合数学)

### 题意

&emsp;&emsp;有 $1~ m$ 个数，组成一个含有$n$个元素的列表，其中 $n-1$个数必须互不相同，剩下一个数出现两次，最大的数必须在列表中间，在它前面的部分严格升序排列，在它后面的部分严格降序排列，求一共有多少个不同的满足要求的列表

### 思路

&emsp;&emsp;将几个条件分开来考虑

- 在 m 个数中选择 $n-1$个数，一共有 $C_m^{\,n-1}$  种情况 
- 相同的数可以和 $n-1$ 个数中除了最大值的任意一个数相同，因为如果最大值重复两遍，两个最大值都必须在中间，无法满足严格升序降序排列，其他值重复可以分别放在最大值两边，故一共有 $n-2$ 种情况
- 当 n 个数选好后，最大值的位置无法选择，重复数则在最大值左右两边各有一个，剩下 $n - 3$ 个数可以任意选择在最大值左边或者右边，故有 $2^{n-3}$ 种 

&emsp;&emsp;最后答案即为所有情况的成绩 $ans=C^{\,n-1}_m \times (n-2) \times 2^{n-3}$ 

&emsp;&emsp;求组和数时，因为模数是质数，直接利用公式暴力求解，用乘法逆元处理除法求模

### 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long LL;
LL n,m;
const LL mod = 998244353;
LL qpow(LL a,LL b)
{
    LL res = 1;
    while(b>0)
    {
        if(b & 1) res = res * a % mod;
        a = a * a % mod;
        b >>= 1;
    }
    return res;
}
LL comb(LL n,LL m)
{
    LL res = 1;
    LL tmp = 1;
    for(int i = 1; i <= n; i++) res = res * i % mod;
    for(int i = 1; i <= m; i++) tmp = tmp * i % mod;
    for(int i = 1; i <= n-m; i++) tmp = tmp * i % mod;
    return res * qpow(tmp, mod-2) % mod;
}
int main()
{
    cin.sync_with_stdio(false);
    cin.tie(nullptr);
    cin >> n >> m;
    LL ans = comb(m, n-1) * (n-2) % mod * qpow(2,n-3) % mod;
    cout << ans << endl;
}
```

