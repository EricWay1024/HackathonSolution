# 2019年编程码拉松题解

宁波诺丁汉大学计算机爱好者协会

## 注意

1. 本题解示例程序语言主要是C++，部分题目只有或兼有Python。

2. 本题解C++的程序大量使用了手写的快速读入函数`int read()`，这实际上可能是不必要的。

3. 本题解C++的程序`main()`函数部分可能省略`return 0`，这是由于作者的懒惰，见谅。

4. 本题解主要侧重于解题的思路，而没有着重讲解编程语言的相关特性。

5. 本题解的题目排列顺序基本上纯看题解写作者的心情，请使用查找功能找到你要的题目。

6. 本题解没有写的比赛题目，为2位作者都不会做的题目，请不要向我们询问。

7. **本队所有题目由 韦宇航 和 陈泉文 完成。**

   作者的联系方式：smyyw8@nottingham.edu.cn, scyqc4@nottingham.edu.cn

**下面的题解部分是由 韦宇航 写成的。**


## 3. Free score

这题只需看明白所有满足条件的字符串所满足的要求。题目有3个要求。

首先，要求1是非常浅易的。

下面，只需把要求2和要求3结合起来。要求2指出`xCPUx`合法，注意前后两个`x`字符串应该保持一致。

在要求3中，

1. 令`a=x, b='P', c=x`， 则可以推出`xCPPUxx`合法。

2. 令`a=x, b='PP', c=xx`，则可以推出`xCPPPUxxx`合法。
3. 以此类推。

进而不难观察发现，并且严格来说也不难使用数学归纳法证明，原题的要求等价于：

1. 字符串含有一个C和一个U，并且C在U前面，剩下的所有字符都是P。
2. 如果假设在C的前面有$p_1$个P， 在C和U之间有$p_2$个P，在U后面有$p_3$个P，则$p_3=p_1p_2$并且$p_2 >0$

从而可以得到如下代码：

```C++
#include<bits/stdc++.h>
using namespace std;
inline int read(){int x=0; int sign=1; register char c=getchar();while(c>'9'||c<'0'){if(c=='-')sign=-1; c=getchar();}while(c>='0'&&c<='9'){x=(x<<3)+(x<<1)+c-'0'; c=getchar();}return x*sign;}

int main(){
	int n=read();
	while (n--){
		string s;
		cin>>s;
		bool flag = true; //字符串是否符合要求
		int c = -1;//记录C的位置
		int u = -1;//记录U的位置
		for(int i=0; i<s.length(); i++){
			if (s[i] != 'C' && s[i] != 'P' && s[i] != 'U') {flag = 0; break;} 
			if (s[i] == 'C') {
				if (c == -1) c = i;
				else {flag = 0; break;} 
			}
			if (s[i] == 'U') {
				if (u == -1) u = i;
				else {flag = 0; break;} 
			}
		}
		if (c>u) {flag = 0;} 
		
		int p1 = c, p2 = u - c - 1, p3 = s.length() - u - 1;
		if (p3 != p1 * p2) flag = 0;
		if (p2 == 0) flag = 0;
		cout<<(flag? "YES": "NO")<<"\n"; 
		
	}
			 

}
```

## 19. Bulb Switcher

显然，这道题可以用模拟的方法，但那太麻烦了。

考虑编号为$t$的灯被开关了多少次。当然，事实上，我们想知道的是这个次数的**奇偶**，因为开关次数的奇偶性影响着灯最后的状态。

如果某一次编号为$t$的灯被打开或关上，那么这一次一定在是开关编号是$t$**的某个因数**的所有倍数的灯。

也就是说，$t$有多少个因数，$t$就要被打开或关上多少次。

那么$t$的因数的个数的奇偶就成为了解决这个问题的关键。

小学数学告诉我们，$a$如果是$t$的因数，那么$t/a$显然是一个整数，且也应该是$t$的因数。那么，

- 如果对于任意$a$，$a \not = t/a$恒成立，显然$t$应该有偶数个因数。
- 如果存在$a$，$a=t/a$成立，那么这样的$a$显然至多只有一个$\sqrt{t}$，此时$t$有奇数个因数。

可以证明，**一个正整数的因数有奇数个**的充要条件是**它是一个完全平方数**。

也就是说，在$1<=t<=n$的所有$t$中，有多少个完全平方数，最后就有多少个灯是开着的。

不难发现，答案是$[\sqrt n]$，其中$[x]$表示高斯函数（不超过$n$的最大整数）。

```C++
#include<bits/stdc++.h>
int main(){
	int n;
    cin>>n;
	cout<<(int)sqrt(n);
    return 0;
}
```

当然，如果先写出一个模拟的程序，再输出一定范围（比如前100个正整数）的$n$对应的答案，也不难观察得出上述结论。下面给出一个模拟程序的代码。

```C++
#include<bits/stdc++.h>
#define N 1120000
using namespace std;
bool bulb[N];
int ans;
int main(){
	int n; cin>>n;
	for(int i=1; i<=n; i++)
		for(int j=i; j<=n; j+=i)
			bulb[j] = !bulb[j];	
	for(int i=1; i<=n; i++) ans+=bulb[i];
	cout<<ans<<endl;
}

```

## 24. Code Word

这题所使用的算法是动态规划。如果您未曾听过这个概念，您可以认为这是一种高维度的递推方法。

题目的意思是：给出一个`r*c`的密码键盘，给出符合条件的定长的密码个数，条件是：

- 密码相邻的两位的任何一位都不能在以对方为中心的九宫格内

我们不妨设函数$f(l,x,y)$表示长度为$l$，且最后一位恰好按在坐标是$(x,y)$的按键上的密码的个数。

让我们考虑$l-1$和$l$的递推关系。如果考虑一个长度为$l$，且最后一位按在$(x,y)$的字符串，那么它的倒数第二位一定不能在以$(x,y)$为中心的九宫格内。也就是说，去掉这个长度为$l$的字符串最后一位，我们将得到的长度为$l-1$的字符串的最后一位，只能在以$(x,y)$为中心的九宫格以外。这样，我们就得到了一个递推关系：
$$
f(l,x,y) = \sum _{(x',y')不在以(x,y)为中心的九宫格内}f(l-1,x',y') 
\\= \sum _{所有(x',y')} f(l-1, x',y') -\sum_{(x',y')在以(x,y)为中心的九宫格内} f(l-1,x',y')
$$
第二个等式是必要的，因为这种变形可以减少运算的时间复杂度。

以及边界条件是：$当l=1时，f(l,x,y)显然为1$.

最后本题的答案，显然是
$$
\sum _{所有(x,y)} f(l, x,y)
$$
另外，本题要求答案对大数取模，根据模数运算的性质，只需要每一步加减乘运算都取模即可。下面的代码中使用了网上的一个模数运算的模板。之所以这么处理，主要归咎于本文作者的懒惰。

从而以下代码就呼之欲出了：

```C++
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;

// auto mod int
// https://youtu.be/L8grWxBlIZ4?t=9858
// https://youtu.be/ERZuLAxZffQ?t=4807 : optimize
// https://youtu.be/8uowVvQ_-Mo?t=1329 : division
const int mod = 1000000007;
struct mint {
  ll x; // typedef long long ll;
  mint(ll x=0):x((x%mod+mod)%mod){}
  mint& operator+=(const mint a) {
    if ((x += a.x) >= mod) x -= mod;
    return *this;
  }
  mint& operator-=(const mint a) {
    if ((x += mod-a.x) >= mod) x -= mod;
    return *this;
  }
  mint& operator*=(const mint a) {
    (x *= a.x) %= mod;
    return *this;
  }
  mint operator+(const mint a) const {
    mint res(*this);
    return res+=a;
  }
  mint operator-(const mint a) const {
    mint res(*this);
    return res-=a;
  }
  mint operator*(const mint a) const {
    mint res(*this);
    return res*=a;
  }
  mint pow(ll t) const {
    if (!t) return 1;
    mint a = pow(t>>1);
    a *= a;
    if (t&1) a *= *this;
    return a;
  }

  // for prime mod
  mint inv() const {
    return pow(mod-2);
  }
  mint& operator/=(const mint a) {
    return (*this) *= a.inv();
  }
  mint operator/(const mint a) const {
    mint res(*this);
    return res/=a;
  }
};

int r, c, l;

mint dp[205][205][205];
mint sum[205]; //以l为长度的密码个数

int main(){
	cin>>r>>c>>l;
	for (int x = 1; x <= r; x++){
		for (int y = 1; y <= c; y++){
			dp[1][x][y] = 1;
		}
	}
	sum[1] = r * c;
	for (int i  = 2; i <= l; i++){
		for (int x = 1; x <= r; x++){
			for (int y = 1; y <= c; y++){
				mint surr = 0; //surrounding
				for (int s = -1 ; s <= 1; s++){
					for (int t = -1; t <= 1; t++){
						surr += dp[i-1][x+s][y+t];
					}
				}
				dp[i][x][y] = sum[i-1] - surr;
				sum[i] += dp[i][x][y];
			}
		}
	}
	cout<<sum[l].x;
}

```

## 10. Jackpot

本题的主要难度在于对题目的理解。实际上，本题的意思是：参赛者可以首先要求排除$d$扇没有奖品的门，代价是以给定的函数降低奖品的金额，然后在剩下的所有门中选择一扇打开，如果有奖品则拿走，没有就结束。

因此，不难发现，参赛者所得到的奖品的金额的数学期望是：
$$
E(d) = \frac {P_R} {n-d} = \frac {m-(df)^2}{n-d}
$$
其中整数自变量满足$0<=d<=n-1$。

如果您的微积分基础足够扎实，您会对上述函数进行求导，然后在O(1)时间内得到本题的答案。（事实上因为这是一个数列而不是定义域为R的函数，理论上也可以用后项减前项来求得单调性。）这的确是一个精妙绝伦的解法，但本文作者并没有这么去写。

如果您的观察能力足够细致，您会发现本题（和此次比赛的许多其他题目不同）给出了$n$的数据范围，并且这个范围并不大。也就是说，枚举所有$d$的可能值，取其中函数值最大的那个，即可轻松地求出本题的答案。

本题答案，根据主办方的调整，最后以保留三位小数为准。

本题的Python代码如下。

```python
def E(d):
    global m, f, n
    return (m - d * d * f * f) / (n - d)
n, m, f = map(float, input().split())
ans = 0
for d in range(int(n)):
	ans = max(ans, E(d))
print("%.3f" % ans)
```

C++版本的代码如下。

```C++
#include<bits/stdc++.h>
using namespace std;
int m, n; 
double f;
double E(double d){
    return (m - d * d * f * f) / (n - d);
}
int main(){
    cin>>n>>m>>f;
    double ans = 0;
    for(int d=0; d<n; d++){
        ans = max(ans, E(d));
    }
    printf("%.3f\n", ans);
    return 0;
}
    
```

## 14. Hamster Ball

这道题希望你修复尽可能多的球，所以，显然你应该从尽可能小的球开始修复。

把所有的球按半径从小到大进行排序。依次对球进行考虑。要么把当前最小半径的球全部修复完，要么把胶带用光。

简短如斯，这题的思路至此告结。

如果还要我说些什么，“贪心算法”。

```C++
#include<bits/stdc++.h>
#define N  123
#define For(i, n) for(int i=0; i<n; i++)
inline int read(){int x=0; int sign=1; register char c=getchar();while(c>'9'||c<'0'){if(c=='-')sign=-1; c=getchar();}while(c>='0'&&c<='9'){x=(x<<3)+(x<<1)+c-'0'; c=getchar();}return x*sign;}
using namespace std;
int l, b;

struct BALL{
	int r, s; //radius, number
	double c; //circumference
}ball[N];

bool cmp(BALL &a, BALL &b){
	return a.r < b.r;
}

int main(){
	l = read(); b = read();
	for(int i=1; i<=b; i++){
		ball[i].s = read(); ball[i].r = read();
		ball[i].c = (double) ball[i].r * 6.28;
	}
	sort(ball+1, ball+1+b, cmp);
	int cnt = 0;
	for(int i=1; i<=b; i++){
		int can_fix = (double) l / ball[i].c; //不考虑球的实际数量，剩余的胶带至多能修复多少当前尺寸的球
		if (can_fix == 0) break; //要是一个都修复不了了，后面也没戏了
		int fixed = min(can_fix, ball[i].s); //实际修复的数量不能超过当前尺寸的球的实际数量
		l -= fixed * ball[i].c; //把胶带用去
		cnt += fixed; //计数
	}
		
	cout<<cnt;
}

```

## 7. Hackathon Attendance

根据题目的要求来就行了。

如果A出现了至少两次，或者有至少三个L相连，那么不合法。

相信优秀的您对字符串的处理还是得心应手的。

```C++
#include<bits/stdc++.h>
using namespace std;

string s;

int main(){
	cin>>s;
	int cnt_a = 0;
	for(int i=0; i<s.length(); ++i){
		if (s[i] == 'A') cnt_a++;
		if (cnt_a > 1) break;
	}
	for(int i=0; i<s.length()-2; ++i){
		if (s[i] == 'L' && s[i+1] == 'L' && s[i+2] == 'L') 
        	{cnt_a = 2; 
             break;}
	}
	if (cnt_a > 1) cout<<"False";
	else cout<<"True";
}

```
当然其实C++有相关的内置函数，但作者不想改了，留给读者。大概也类似Python的这些写法。

```python
s = input()
cnt_a = s.count('A')
find_l = s.find('LLL')
if cnt_a > 1 or find_l != -1:
    print('False')
else:
    print("True")
```

## 6. Heaters

显然，每个房子都需要找到离它最近的那个供暖设施。有下面几种情况：

1. 房子在所有供暖设施的左边，那么离房子最近的供暖设施就是最左边那个。
2. 房子在所有供暖设施的右边，那么离房子最近的供暖设施就是最右边那个。
3. 房子在某两个供暖设施之间，那么比较一下房子到左右两边两个供暖设施的距离即可。

另一方面，如果我的房子在你的房子右边，那么距离我的房子最近的供暖设施肯定不会在距离你的房子最近的供暖设施的左边。因此，如果从左往右扫描所有的房子，那么对应的离房子最近的供暖设施绝对不可能向左移动，而只能相应地保持不动或者向右移动。

因此，如果是上述第3种情况，当扫描的房子向右移动的时候，适当地向右移动对应的供暖设施，即可让房子处于两个供暖设施之间。

至于前面2种情形，则需要一些特殊的判断。

当每个房子都找到离它最近的供暖设施后，算出距离，取所有距离的最大值，就是我们需要的答案。

```C++
#include<bits/stdc++.h>
#define N 25200
using namespace std;
double house[N];
double heat[N];
int n, m;
double dis_max;

int main(){
	cin>>n;
	for(int i=0; i<n; i++) {
		scanf("%lf", &house[i]); 
	}
	cin>>m;
	for(int i=0; i<m; i++){
		scanf("%lf", &heat[i]);
	}
	sort(house, house+n);
	sort(heat, heat+m);
	int s=0, t=0;
	for(; s<n; s++){
		while(1) {//适当地移动所扫描的供暖设施
			if (t ==  m-1) break; //无法继续向右移动
			if (heat[t] >= house[s]) break; //house[s]恰好移动到heat[t-1]和heat[t]之间
			else t++;
		}
		if (heat[t] == house[s]) continue;
		if (t==0) dis_max = max(dis_max, abs(house[s] - heat[t])); //上述第1种情形
		else{
			double dis_left = house[s] - heat[t-1];
			double dis_right = heat[t] - house[s];
			if (dis_right < 0) dis_max = max(dis_max, -dis_right); //上述第2种情形
			else{//第3种情形
				if (dis_left < dis_right) {
					t = t-1;
					dis_max = max(dis_max, dis_left);
				} else {
					dis_max = max(dis_max, dis_right);
				}
			}
		}
	}
	cout<<dis_max;
}

```

## 9. Best Time to Buy and Sell IWA

*提示：为了避免学术不端行为，请勿买卖并请独立完成IWA。本题解将IWA改写为“物品”。*

如果我在某一天买入了物品，我当然要在这一天之后物品价格最高的那一天把物品卖出去。

我因此需要知道在任意一天之后（含）的所有天里物品价格的最大值。而今天之后物品价格的最大值，显然是明天之后物品价格的最大值，和今天物品的价格之中较大的那个。从后往前递推即可。

```C++
#include<bits/stdc++.h>
#define N 1123456 
using namespace std;
inline int read(){int x=0; int sign=1; register char c=getchar();while(c>'9'||c<'0'){if(c=='-')sign=-1; c=getchar();}while(c>='0'&&c<='9'){x=(x<<3)+(x<<1)+c-'0'; c=getchar();}return x*sign;}

int price[N];
int n;
int dp[N]; //在第i, i+1, ... , n这些天里面的最大值是多少 
int max_prft;
int main(){
	n = read();
	for(int i=0; i<n; i++) price[i] = read();
	for(int i=n-1; i>=0; --i){
		dp[i] = max(price[i], dp[i+1]);
	}
	for(int i=0; i<n; i++){
		if (dp[i+1]-price[i] > max_prft) {
			max_prft = dp[i+1] - price[i];
		}
	}
	cout<<max_prft;
}

```

## -1. Easy But Not Hello World

C++

1. 除数为0的特判。
2. 转换成 double 类型。否则，因为两个`int`类型进行除法，其结果仍然是一个整型变量，就会造成错误的结果。

```C++
#include<bits/stdc++.h>
using namespace std;
int main(){
	cin>>a>>b;
	if (b == 0) cout<<"error";
	else cout << (double)a/b;
	return 0;
}
```

Python3

除法默认是转换为浮点数类型。如果要整数除法，用`//`运算符。

```Python
a, b = map(int, input().split())
if b == 0: print('error')
else： print(a/b)
```

**下面开始的题解部分由 陈泉文 完成。**

## 0. HelloWorld
这道题没有题解，只有代码。
```C++
#include <bits/stdc++.h>
using namespace std;
int main()
{
    cout << "Hello World!";
    return 0;
}
```
事实上这道题目是我第一时间做的题目，由于太过紧张，提交错了两次，原因是自以为是地给两个词语之间加上了逗号，希望给位选手仔细读题，以我为鉴。

下面是Python代码：

```python
print("Hello World!")
```

下面是C语言代码：

```C
#include<stdio.h>
int main(){
	printf("Hello World!");
	return 0;
}
```


## 1. HelloWorld2
同上，这道题目真的不想写什么。

C++

```C++
#include <bits/stdc++.h>
using namespace std;

int main()
{
    int a, b;
    cin >> a >> b;  
    cout << a + b << endl;
    return 0;
}
```

Python

```python
a, b = map(int, input().split())
print(a + b)
```

C

```C
#include<stdio.h>
int main(){
    int a, b;
    scanf("%d%d", &a, &b);
    printf("%d\n", a+b);
    return 0;
}
```

## 11. Last World

这是一道看上去比较怕人，但是稍加思考就会发现很简单的题目。

题目要求我们对一个字符串多次使用`substr`函数，但是事实上完全不用这么做。

不难想到，我们只需要将每次读入的第一个数字相加，因为最终字符串的起点在原字符串中的位置一直在向前移动。对于第二个数字，切割长度，我们只需要使用最后一次的即可。

```C++
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<int, int> pii;

int main()
{
    string s;
    cin >> s;
    int n;
    cin >> n;
    int st = 0;
    int e = s.length() + 1;
    for (int i = 0; i < n; i++)
    {
		int temp1, temp2;
        cin >> temp1 >> temp2;
        st += temp1;
        e = temp2;
    }
    cout << s.substr(st, e) << endl;
    return 0;
}
```

## 2. HelloWorld3
```C++
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<int, int> pii;

int main()
{
    for (int i = 1; i <= 10; i++)
    {
        cout << i << endl;
    }
    return 0;
}
```

## 21. Cute Rabbits and Fib
这是一道找规律的题目，需要在一棵由斐波那契数列构造的树中找到两点的`LCA`。通过打表或者看图例中的规律，不难发现，节点`x`的父亲节点为`x-f[i]`，其中`f[i]`为在该编号前的最大的斐波那契数。例如，对于`10`号节点来说，在`10`之前的最大斐波那契数为`8`，则`10`号节点的父亲节点为`10-8=2`。由此题目已经做完，但是需要注意的是，在寻找最接近的斐波那契数的时候，要使用二分查找，暴力的查找仍会超时。

由于主办方没有给出数据范围，斐波那契数列算到第几项是一个玄学问题，多尝试提交可解。

```C++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;

ll m, f[65];

int main() {
	cin >> m;
	f[0] = f[1] = 1;
	for(int i = 2; i <= 60; i++) {
		f[i] = f[i-1] + f[i-2];
	}
	while (m--) {
		ll x, y;
        cin >> x >> y;
		while (x != y) {
			ll tmp = max(x, y);
			int l = 1, r = 60;
			//二分查找
			while (l <= r) {
				int mid = l + r >> 1;
				if(tmp <= f[mid]) r = mid - 1;
				else l = mid + 1;
			}
			//向上爬树
			if (x > y) x -= f[r];
			else y -= f[r];
		}
		cout << x << endl;
	}
	return 0;
}
```

## 22. Analogue Cluster
DFS 求连通块，然后暴力遍历每一个连通块，找出每一个连通块的众数的个数，然后用连通块的总节点数减去该连通块的众数的个数，最后求和即可。
```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 1010;
int w[N];
bool vis[N];
vector<int> edge[N];
vector<int> inqueue;

void dfs(int x)
{
    vis[x] = true;
	//用inqueue来表示是否在当前的连通块中
    inqueue.push_back(w[x]);
    for (int y : edge[x])
        if (!vis[y])
            dfs(y);
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    cout.tie(NULL);
    int m, n;
    cin >> n >> m;
    for (int i = 0; i < n; i++)
        cin >> w[i];
    for (int i = 0; i < m; i++)
    {
        int u, v;
        cin >> u >> v;
		//注意vector中的编号从0开始，这里需要对读入的数据做一下处理
        u--, v--;
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    int ans = 0;
    for (int i = 1; i < n; i++)
    {
        if (!vis[i])
        {
            dfs(i);
            sort(inqueue.begin(), inqueue.end());
            int counter = 0;
            for (int j = 0; j < inqueue.size(); j++)
            {
                int k = 0;
                while (inqueue[j] != inqueue[k]) k++;
                counter = max(counter, j - k + 1);
            }
            ans += inqueue.size() - counter;
			//不要忘记清空当前的搜索结果
            inqueue.clear();
        }
    }
    cout << ans << endl;
}
```

## 23. Bus Logic
首先我考虑将`01`字符串转换为数字进行处理，以便于利用位运算的一些性质。当然这道题目也可以用字符串模拟完成。由于代码中位运算使用的次数较多，请直接看代码中的注释。
```C++
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<int, int> pii;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(NULL); cout.tie(NULL);
    int m, b, s;
    cin >> m >> b >> s;
    ll ans = 0;
    string st;
    for (int i = 0; i < b; i++)
    {
        ll stop = 0;
        cin >> st;
		//下面这个循环将01字符串转换为数，如有不懂的地方，建议对小数据进行手动模拟
        for (ll j = s; j > 0 ; j--)
            if (st[s - j] == '1') stop += ll(1) << (j - 1);
		
		//只有在起点站这辆Bus的停靠状态为1，我们才将其归入ans的计算
		//这里按位或的使用，避免了对不同Bus达到同样站点的重复计算问题
        if (stop & (ll)1 << (s-m)) ans |= stop;
    }

	//最后我们只需要统计ans中1的个数即可
    int bits = 0;
    for (int i = 0; i < s; i++)
    {
        if (ans & (ll)1 << i)
            bits ++;
    }
	//这里需要-1的原因是需要排除起点站，
	//但注意不要把0改成-1
    cout << max(0, bits - 1) << endl;
}
```


## 25. Cartage
这道题目我们当时做的时候想错了模型，导致没能在比赛时完成。该题为赛后完成的。

考虑到图中有一些权值较小的边，无论在什么样的情况下都不会走过，因此我们可以考虑将其删去。由此我们有了最大生成树的想法，讲图上问题转换为树上问题进行分析。

在转换为树之后，任意两点之间的路径唯一，我们可以用求`LCA`的方法来找到这条路径。在下面的代码中，我用搜索来得到节点的深度信息，然后再进行树上倍增来求得`LCA`。


*我竟然没有发现这是`NOIp 2013`原题。*
```C++
#include <cstdio>
#include <algorithm>
#include <cstring>
using namespace std;

const int maxm = 50005;
const int maxn = 10005;

struct edge{
	int u, v, w;
}e[maxm];

struct s{
	int v, w, next;
}e2[maxm];

bool cmp(edge& a, edge& b){ return a.w > b.w;}

int f[maxn], head[maxn], depth[maxn], fa[maxn][20], d[maxn][20], vis[maxn], n, m, q, cnt = 0, ans = 0, tot = 0;

int find(int x){return f[x] == x ? x : f[x] = find(f[x]); }

void add_edge(int u, int v, int w){
	e2[++cnt].v = v;
	e2[cnt].next = head[u];
	e2[cnt].w = w;
	head[u] = cnt;
}

void Kruskal(){
	sort(e+1, e+m+1, cmp);
	for(int i = 1; i <= n; i++) f[i] = i;
	for(int i = 1; i <= m; i++){
		int x = find(e[i].u), y = find(e[i].v);
		if(x != y){
			add_edge(e[i].u, e[i].v, e[i].w);
			add_edge(e[i].v, e[i].u, e[i].w);
			f[x] = y;
			tot++;
		}
		if(tot == n - 1) return;
	}
}

void dfs(int x){
	vis[x] = 1;
	for(int i = 1; i <= 16; i++){
		if(depth[x] < (1 << i)) break;
		fa[x][i] = fa[fa[x][i-1]][i-1];
		d[x][i] = min(d[x][i-1], d[fa[x][i-1]][i-1]);
	}
	for(int i = head[x]; i; i = e2[i].next){
		if(vis[e2[i].v]) continue;
		fa[e2[i].v][0] = x;
		d[e2[i].v][0] = e2[i].w;
		depth[e2[i].v] = depth[x] + 1;
		dfs(e2[i].v);
	}
}

int lca(int x, int y){
	if(depth[x] < depth[y]) swap(x, y);
	int temp = depth[x] - depth[y];
	for(int i = 0; i <= 16; i++)
		if((1<<i) & temp) x = fa[x][i];
	for(int i = 16; i >= 0; i--)
		if(fa[x][i] != fa[y][i]) {x = fa[x][i]; y = fa[y][i];}
	if(x == y) return x;
	return fa[x][0];
}

int ask(int x, int f){
	int mn = 2147483647;
	int t = depth[x] - depth[f];
	for(int i = 0; i <= 16; i++)
		if(t & (1 << i)) {mn = min(mn, d[x][i]); x = fa[x][i];}
	return mn;
}

int main(){
	scanf("%d%d", &n, &m);
	memset(d, 2147483647, sizeof(d));
	for(int i = 1; i <= n; i++) f[i] = i;
	for(int i = 1; i <= m; i++)
		scanf("%d%d%d", &e[i].u, &e[i].v, &e[i].w);
	Kruskal();
	for(int i = 1; i <= n; i++)
		if(!vis[i]) dfs(i);
	scanf("%d", &q);
	for(int i = 1; i <= q; i++){
		int x, y;
		scanf("%d%d", &x, &y);
		if(find(x) != find(y)) {printf("-1\n"); continue;}
		else{
			int t = lca(x, y);
			printf("%d\n", min(ask(x, t), ask(y, t)));
		}
	}
	return 0;
}
```
当然，对于查询次数比较多的情况，这道题在建完最大生成树之后，也可以使用离线处理的方法。


由于这道题目的权值在边上，我们先考虑把边权转换为点权，可以采用将每条边的权值转移到深度比较大的点上。然后对整棵树进行数链剖分，并用线段树来维护链上最小值即可。
这种方法的具体细节就留给有兴趣的读者自行实现了。


整到题目的难点在于想到最大生成树，后面的处理方式是多样且模板化的。


## 26. Rosy's Doubt
找规律题，通过打表不难发现，答案为`a*b-(a+b)`。

```Python
a, b = map(int, input().split())
print(a*b-a-b)
```
此题为 `NOIp2017 提高组` 原题，下面搬运一下其他网站的数学证明：
https://www.luogu.org/problemnew/solution/P3951


## 2. CPUAwesome
依据题意模拟即可。

```C++
#include<bits/stdc++.h>
using namespace std;
inline int read(){int x=0; int sign=1; register char c=getchar();while(c>'9'||c<'0'){if(c=='-')sign=-1; c=getchar();}while(c>='0'&&c<='9'){x=(x<<3)+(x<<1)+c-'0'; c=getchar();}return x*sign;}

int main(){
	int a = 3, b = 5, n = read();
	for(int i=1; i<=n; i++){
		//15的倍数
		if ((i%a==0) && (i%b)==0) {
			printf("CPUAwesome\n");
			a++; b++;
		} else{
			//3的倍数
			if ((i%a)==0) printf("CPU\n");
			//5的倍数
			if ((i%b)==0) printf("Awesome\n");
			//都不是的情况
			if ((i%a) && (i%b)) printf("%d\n", i);
		}
	}
	return 0;
}
```

## 5. Love
此题应由主办方背锅。


1. 只要字符串中出现了任意形式的 `love` 就把字符串后面的所有东西替换成 `' CPU'`
2. 如果没有出现，则不输出任何信息（这点在题目中没有很好地说明，我们一开始以为是输出原字符串）。

```Python
s = input()
s = str(s)
#利用全部小写，来避免“love”形式不同的问题
t = s.lower()
#当没有找到的时候，a的值为-1
a = t.find('love') + 4
if a != 3: 
	print(s[:a] + ' CPU', end='')
else: 
	pass
```

## 8. sqrt()
题解就像它的标题一样。


有趣的是，此题的解和上面某一题的解是一样的。

```C++
#include <bits/stdc++.h>
using namespace std;

int main()
{
    int x;
    cin >> x;
    cout << int(sqrt(x)) << endl;
    return 0;
}
```

## 一些想法
对于没有出现在上面的题目来说，这里是一些没有经过验证的不负责任的猜想：

1. Kings 没有任何想法。
2. Internet Upload 可能是个图论。考虑到从一个咖啡店出发，要不待到关门，要不在中途某个时间赶往下一个，可以考虑将从一个咖啡店走到另一个咖啡店的可能时间段作为边来建图。将每一个咖啡店依据时间来分成多个小的时间段。但是具体的建图方法我没有想好。
3. Garden Variety Vampire 这辈子都不可能会的计算几何题。
4. Dynamo Wheel 考虑到，每一个桶的状态改变只可能在顶部或者底部。当所有桶的状态固定的时候，其转动一个角度带来的质心变化可以由一个纯粹的数学函数来表示。感觉上来说，可以在求得函数之后，可以通过求导或者搜索的方式获取到每一个小段的最大值，最后多个段的结果求最大即可。但是数学函数的推导超出了本题解作者的能力范围。
5. Evenly Divided 感觉贪心可解。
6. Fibonacci Compression 不能每一次新的元素进来都重新排序，或许需要维护一个数据结构，能够动态处理每一个新进来的元素对原有排序结果的改变。