# 0903模拟赛题解


## A. Comfortable Cows

$\quad$[题目传送门](https://www.luogu.com.cn/problem/P9944)



### 简化题意

​	给定每个奶牛的坐标，求每输入一个奶牛，草地上一共有多少个 **舒适的** 奶牛。

> 什么是舒适？
>
> ​	当与这头奶牛相邻的四个点 (上下左右) 中 **有且仅有** $3$ 头奶牛时，这头奶牛就是舒适的。
>
> ​	特别的，如果相邻有 $4$ 头奶牛，这头奶牛 **不舒适**。



### 48pts思路

​	我们考虑每输入一个奶牛，就把之前的奶牛全部遍历，看有几个 舒适的 奶牛。

​	如果是整地图遍历，(从最小点遍历到最大点)，时间复杂度 $\Omicron(n^3)$。

​	如果是每次遍历每个奶牛坐标，时间复杂度 $\Omicron(n^2)$，期望得分 $48 ~ pts$。



### 正解

​	通过分析上面的思路，发现实际上有很多遍历都是无用的。

​	简单推导一下，发现 一个奶牛加入后，能影响的奶牛只有这头奶牛的上下左右 $4$ 个格子 以及 它本身，也就是 $(x,y) ~,~ (x-1,y) ~,~ (x+1,y) ~,~ (x,y-1) ~,~ (x,y+1)$。

​	那么我们每输入一个奶牛，也只需要判断它周围的这 $4$ 个格子是否受影响。

​	有两种情况： 舒适 $\rightarrow$ 不舒适 (从 $3$ 个到 $4$ 个) ，不舒适 $\rightarrow$ 舒适 ( $1,2$ 到 $3$ )，只需要分别判断并修改答案即可。

​	时间复杂度 $\Omicron(N)$ 。 



### Code

```cpp
//10^5 优化 fixed.
#include<iostream>
#include<stdio.h>
#include<algorithm>
#include<climits>
using namespace std;

//输入的奶牛 x,y 
int x[100005] = {};
int y[100005] = {};
bool mark[1005][1005] = {};//标记当前位置是否有奶牛 
long long sum[1005][1005] = {};//当前位置周围的奶牛数量 
long long n,cnt=0,ans=0;

inline bool check(int chx,int chy){//检查位置是否合法 && 当前位置是否有奶牛 
	if(chx>=0 && chx<=n && chy>=0 && chy<=n){
		if(mark[chx][chy]==true){
			return true;
		}
	}
	return false;
}

int main(){
	
	freopen("comfortableb.in","r",stdin);
	freopen("comfortableb.out","w",stdout);
	
	cin>>n;
	for(int i=1;i<=n;++i){
		cin>>x[i]>>y[i];
		mark[x[i]][y[i]] = true;//标记这个位置有奶牛 
		
		//开始向 4 个方向判断 
		if(check(x[i]-1,y[i])){//上，如果满足条件
			sum[x[i]-1][y[i]]++,sum[x[i]][y[i]]++;//两个奶牛周围奶牛数都要加上 
			if(sum[x[i]-1][y[i]]==3){//如果上面这头奶牛舒适了 
				ans++;//舒适奶牛个数 ++
			}
			else if(sum[x[i]-1][y[i]]>3){//如果因为这头奶牛，上面奶牛不舒适了 
				ans--;//从ans剔除掉 
			}
			//注意这里不能直接写 else,因为可能sum < 3,此时还没有达到舒适条件，也就不能减去 
		}
		if(check(x[i]+1,y[i])){//剩下的 3 个方向同理 
			sum[x[i]+1][y[i]]++,sum[x[i]][y[i]]++;
			if(sum[x[i]+1][y[i]]==3){
				ans++;
			}
			else if(sum[x[i]+1][y[i]]>3){
				ans--;
			}
		}
		if(check(x[i],y[i]-1)){
			sum[x[i]][y[i]-1]++,sum[x[i]][y[i]]++;
			if(sum[x[i]][y[i]-1]==3){
				ans++;
			}
			else if(sum[x[i]][y[i]-1]>3){
				ans--;
			}
		}
		if(check(x[i],y[i]+1)){
			sum[x[i]][y[i]+1]++,sum[x[i]][y[i]]++;
			if(sum[x[i]][y[i]+1]==3){
				ans++;
			}
			else if(sum[x[i]][y[i]+1]>3){
				ans--;
			}
		}
		if(sum[x[i]][y[i]]==3){//特判一下如果当前奶牛舒适 
			ans++;
		}
		cout<<ans<<"\n";//输出答案 
	}
	return 0;
}
```





## B. Just Stalling

​	[题目传送~](https://www.luogu.com.cn/problem/P9942)



### 题意简述

​	有 $N$ 头奶牛和牛棚，每头奶牛有自己的身高，每个牛棚也有限高，求在让每头奶牛都住进牛棚的情况下，方案有多少种。注意答案可能 **超出 `int`** 范围，请使用 `long long` 。



### 暴力思路

​	既然是求奶牛住宿方案，最暴力的思路 ~~当然~~ 是直接使用全排列的思想，递归求解每次排列是否合法。

​	时间复杂度 $\Omicron(N!N)$ 或者 $O(N!)$，期望得分 $40$ 分。

​	~~[浅浅给一个递归实现代码](https://www.luogu.com.cn/paste/yryq4cnp)~~



### 正解

​	我们把 $a$ 和 $b$ 数组从小到大排序，从前往后找每一头奶牛最多可以住到多少牛棚，记为 $sum_i$ 。

​	随后从大到小遍历 $sum$ ，根据乘法原理，$sum_n$ 的方案数是 $sum_n - (n-n) \Rightarrow sum_n$  ，$sum_{n-1}$ 的方案数是 $sum_{n-1} - (n-(n-1)) \Rightarrow sum_{n-1} - 1$   (因为前面的第 $n$ 头牛已经占用了一个牛棚)   ，$\dots$ ，$sum_1$ 的方案数是 $sum_1 - (n-1)$。

​	 以此类推， 可以推出 $sum_i = sum_i - (n-i)$。

​	最后把 $sum_i$ 乘起来既是最终答案。

​	

​	这里寻找 "每一头牛最多可以住到多少牛棚" 时，可以使用 $ \Omicron(n^{2}) $  的循环双层遍历寻找，也可以使用二分查找，时间复杂度 $\Omicron(n\log n)$ 

​	(虽然 $N$ 只有 $20$ 无所谓就是了)

### Code

​	给出的代码使用二分查找方法。

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
//输入的 a,b
int a[105] = {};
int b[105] = {};
long long sum[105] = {};//记录每个牛住的牛棚方案数
long long ans = 1;
int main(){
	
	freopen("stalling.in","r",stdin);
	freopen("stalling.out","w",stdout);
	
	int n;
	cin>>n;
	for(int i=1;i<=n;++i){
		cin>>a[i];
	}
	for(int i=1;i<=n;++i){
		cin>>b[i];
	}
	//两个数组从小到大排序 
	sort(a+1,a+n+1);
	sort(b+1,b+n+1);
	for(int i=1;i<=n;++i){
		//这里的 sum[i] 记录的是方案数，先找到第一个大于等于a[i]的数的下标，用 n - 下标 + 1即是可以用的方案 
		sum[i] = n-(lower_bound(b+1,b+n+1,a[i])-b)+1;
	}
	for(int i=n;i>=1;--i){//乘法原理乘起来
		ans *= sum[i]-(n-i);
	}
	cout<<ans<<"\n";
	return 0;
}
```







## C. Comfortable Cows 2

​	[题目传送~](https://www.luogu.com.cn/problem/P7411)

​	[$A$题在Hydro上的地址](https://hydro.ac/d/cqyz/p/318)



### 题意简述

​	给定每个奶牛的坐标，求每输入一个奶牛，使得平面上不存在 **舒适的** 奶牛，至少需要增加几头奶牛？

​	题目满足 $0 \leqslant$ 每个奶牛的坐标 $\leqslant ~ 1000$ ，但增加的奶牛坐标可能 **不在** $0 ~ \sim ~ 1000$ 范围内。

> 如果你不知道什么是 **舒适的** ，请前往 $A$ 题查看。



### 解题思路

​	这道题实际上是 $A$ 题的升级版，所以思路也可以借鉴 $A$ 题的结论。

​	但是需要注意的细节和坑可远远比 $A$ 题多得多，~~调了我整整一天啊啊啊~~ 

​	首先，借用 $A$ 题的结论，一头奶牛放进去后，受影响的只有它本身和它周围上下左右 $4$ 个点。也就是 $(x,y) ~,~ (x-1,y) ~,~ (x+1,y) ~,~ (x,y-1) ~,~ (x,y+1)$ 。

​	那么 受到需要增加的奶牛的影响 的那些奶牛也就是增加奶牛的上下左右 $4$ 个点。

​	什么情况需要增加奶牛呢？很显然，要从 **舒适** 变得 **不舒适** ，就需要它周围的奶牛数从 $3 \rightarrow 4$ ，也就是在上下左右空闲区域加一头奶牛。

​	$\therefore$  ，我们对于每一个 **舒适的** 奶牛，都可以在它周围空闲的位置加一头奶牛，就能变成 **不舒适** 。

​	但是这样做，可能会引发连锁反应。也就是本来有一头牛不舒适，但是你为了让另外一头牛舒适，加了一头牛，这头牛就变舒适了。

​	这时，你只能再去找有没有哪头牛舒适，再把它变为不舒适。(每次访问每头牛下标，看它是否舒适)

​	这样一来，考虑最坏情况的单次更改时间复杂度是 $\Omicron(1000^4)$ ！

​	这还只是一次让一头牛变成不舒适的时间复杂度，还需要 $\times n\dots$

​	如何优化呢？



### 优化思路

​	我们可以考虑使用 $BFS$ 的思想，用一个队列来记录 **需要增加的** 奶牛的下标，每次获取这个下标，再找它四周会不会因为它产生连锁反应。如果会，再入队 **因为它而需要增加的** 奶牛下标。以此类推，直到没有 需要增加的奶牛为止。具体步骤如下：

1. 以输入的奶牛为基准，找到需要增加的奶牛下标，并入队。(具体流程与下文相似) ， 注意此时 **不需要算在** 需要增加的总数内，后续统一相加。

2. 开始循环，直到队列为空：
   - 获取队列队顶坐标
   
   - 将 $mark$ 数组当前坐标设为 `true` (表示这个位置已经有牛了)  
   
   - `ans++`，表示这里有一头需要增加的牛
   
   - 循环遍历五个方向 (此时自身 **也要** 算在内！)
     - 根据遍历出来的坐标，再循环遍历 $4$ 个方向 (此时 **没有** 自身，不然会死循环 $\rightarrow$ 反复和当前位置横跳 )
       - 如果周围有牛，`cnt++`
       
       - 否则，记录当前位置的下标 (为后面的 入队操作 记录)
       
         
       
     - 循环完后，检查 `cnt` 是否为 $3$ (是否舒适)
     
       - 如果舒适，那么将之前记录的下标 入队 (这里需要增加牛，才能让当前牛舒适)



​	$one ~ last ~ question$，什么时候需要 `ans--` 呢？

​	当输入的奶牛下标在 $mark$ 数组中 == `true` 时，便需要 `ans--` 。

​	因为题目保证奶牛不会重复出现在同一坐标，如果没输入的奶牛却标记为有，那么必然是因为之前 "舒适 $\rightarrow$ 不舒适" 过程中添加了这一奶牛，但输入后表示本来这里就有一头，也就不需要加到 `ans` 里。 



> 注意一个小点，题目中说  “增加的奶牛坐标可能 **不在** $0 ~ \sim ~ 1000$ 范围内”  ，所以这里的 $mark$ 不能用普通的数组存储 (产生负数越界 $RE$ ) ，这里我选择的是用 `map<pair<int,int>> mark`  来存储。
>
> 当然，这里也可以用将坐标全部增加一个 **所有坐标都不能到达的数** ，这样就可以用普通数组存放了。



### 坑点

​	这里汇总了基本所有我遇到过的小细节、注意事项和坑点。

1. 是在 "优化思路" 里说过的，**初始化队列(通过输入坐标来入队)** 的循环 和 之后**寻找队首坐标附近是否有舒适** 中，均需要算上本身，因为它本身有可能也是 **舒适** 的，需要添加奶牛使得它 **不舒适** 。

   > 注意这里的 **里层循环不需要算它本身** ，否则可能会出现反复横跳 (入队当前点，并且无限入队)

2. 对于每个点，需要先满足这个点 **有奶牛**  (也就是 `mark[x][y]==true` )，才能进行下一步操作 (入队、`cnt++` $\dots$ )
3. 对于每个在队列中的点，在开始循环遍历方向之前需要特判一下，如果这个点已经有奶牛，说明前面有一次已经被遍历过了，此时 不需要，也不能再遍历 (否则可能会导致答案多记录) ，直接 `continue` 即可。
4. 注意在每一次寻找时，$ans$ 是 **不被清空** 的，因为可能之前有一个点需要增加，但不在当前点的覆盖范围内，会漏算。
5. 本题数据量较大，请使用 `scanf` 和 `printf` 进行读写，否则会超时。



### Code

​	请再次注意，本题数据量较大 (达到 $10^6$ 级别) ，请使用 `scanf` 和 `printf` 进行读写操作，使用 `cin` 和 `cout` 进行读写会超时。

```cpp
#include<iostream>
#include<stdio.h>
#include<map>
#include<queue>
using namespace std;

struct cow{//为队列服务，分别存放奶牛的 x,y
	int x;
	int y;
};

int x[100005] = {};//输入的 x,y
int y[100005] = {};
map<pair<int,int>,bool> mark;//标记数组，标记i,j是否出现奶牛 
int fangx[15] = {-1,1,0,0,0};//方向数组，代表 上 下 左 右 本身 
int fangy[15] = {0,0,-1,1,0};
queue<cow> que;//队列，用来存放需要添加的奶牛 

int n,cnt=0,qx=0,qy=0;
int ans = 0;

inline void fun(int num){
	cnt = 0,qx = 0,qy = 0;//初始化 
	
	for(int i=0;i<5;++i){//上 下 左 右 本身 五个方向循环 
		cnt = 0;
		int bfsx = x[num]+fangx[i];
		int bfsy = y[num]+fangy[i];
		if(mark[{bfsx,bfsy}]==true){//如果这个点有奶牛 
			for(int j=0;j<4;++j){//以这个点为起始点向四周寻找 (这里不需要本身) 
				int aroundx = bfsx+fangx[j];
				int aroundy = bfsy+fangy[j];
				if(mark[{aroundx,aroundy}]==true){//如果这个点旁边有奶牛 
					cnt++;
				}
				else{//否则，记录没有奶牛的那个点，方便后面 push
					qx = aroundx,qy = aroundy;
				}
			}
			if(cnt==3){//如果这个点附近有 3 头奶牛
				que.push({qx,qy});//把需要添加的坐标入队 
			}	
		}
	} 
	
	while(!que.empty()){//只要队列不为空 
		//获取队首，出队
		int frx = que.front().x , fry = que.front().y;
		que.pop();
		
		//如果这里没有奶牛，ans++，需要增加 
		//否则表示之前已经遍历过，直接continue
		if(mark[{frx,fry}]==0) ans++;
		else continue;
		mark[{frx,fry}] = true;//标记当前位置有奶牛 
		
		for(int i=0;i<5;++i){//如上，五个方向循环
			int bfscnt = 0,errorx=0,errory=0;
			int bfsx = frx+fangx[i];
			int bfsy = fry+fangy[i];
			
			if(mark[{bfsx,bfsy}]==true){//当前点有奶牛
				for(int j=0;j<4;++j){
					int aroundx = bfsx+fangx[j];
					int aroundy = bfsy+fangy[j];
					if(mark[{aroundx,aroundy}]==true){//当前点四周有奶牛
						bfscnt++;
					}
					else{//没有奶牛，记录下标 
						errorx = aroundx,errory = aroundy;
					}
				}
				if(bfscnt==3){//如果周围有 3 头，入队
					que.push({errorx,errory});
				}			
			}
			
		}
		
	}
	
	printf("%d\n",ans);//输出答案 
}

int main(){

	freopen("comfortables.in","r",stdin);
	freopen("comfortables.out","w",stdout);
	
	//使用scanf读入 
	scanf("%d",&n);
	for(int i=1;i<=n;++i){
		scanf("%d%d",&x[i],&y[i]);
		if(mark[{x[i],y[i]}]){//如果当前mark里有奶牛 
			ans--;//不需要这头增加的奶牛,ans--
		}else{
			mark[{x[i],y[i]}] = true;//标记当前奶牛存在 
		}
		fun(i);//开始循环遍历 
	}
	
	return 0;
}
```






## D. Year of the Cow

[题目传送门](https://www.luogu.com.cn/problem/P7412)



### 题意简述

​	奶牛 $Bessie$ 想去拜访祖宗，有每 $12$ 年开放一次的时间机器，可以去往过去或将来的任意一年 (前提是必须是 $12$ 的倍数) 。

​	现在有 $N$ 个祖宗，给出它们生活的时间 (保证不是 $12$ 的倍数) ，一共能用 $K$ 次时间机器，求 $Bessie$ 从去拜访祖宗到回到当前年的最小度过时间是多少？ 

​	

### 解题思路

​	首先我们要明确，什么情况下才能使度过时间最少？ 用画图来模拟一下，以样例为例，画出时间轴。

​	我们考虑一次穿越到最远古的祖先之前的最近时间机器点，再选择没有祖先的地方进行传送，如下图：![D_AC](https://cdn.luogu.com.cn/upload/image_hosting/3nvji8sx.png)

$\\$

​	作为反例，我们考虑先穿越到 $48$ 的点，拜访位于 $46$ 的祖先，再等待到 $36$ 时间机器，穿越到 $108$ 拜访祖先。
​	![D_WA](https://cdn.luogu.com.cn/upload/image_hosting/expvyglf.png)

​	经过计算，我们可以发现第二种方法的耗时要比第一种多，推导一下，得出规律：



​	**$\large 先用时间机器穿越到最远古的祖先之前的最近时间机器点，$**

​	**$\large 再根据 K 的次数，找到最大间隔的那几个空闲时间，$**

​	**$\large 并用总时间减去间隔时间。 $**



​	那如何求最大间隔呢？

​	可以维护一个 $l,r$ ，在每一个时间机器中，如果 $l,r$ 的范围内有祖先，那么 $r-1$ 的那个时间机器到 $l$ 必定是可用的，用一个 $vector$ 记录间隔时间即可 (建议搭配代码食用) 

​	

### Code

​	思路可能会有些没讲清楚，请根据代码参考：

```cpp
#include<iostream>
#include<algorithm>
#include<vector>
using namespace std;
long long a[70005] = {};//输入的祖先生活年份 
long long n,k,mx=-1,ans=0;
vector<long long> vec;//用来记录空闲时间 
int main(){
	
	freopen("cowyears.in","r",stdin);
	freopen("cowyears.out","w",stdout);
	
	cin>>n>>k;
	for(int i=1;i<=n;++i){
		cin>>a[i];
		mx = max(a[i],mx);//记录一下最远古祖先的时间点 
	}
	while(ans<mx){//找到第一个离远古祖先最近的，并且在它之前的时间机器 
		ans += 12;
	}
	k--;//相当于已经用了一次传送了，k--
	if(k==0){//如果 k 没有了
		cout<<ans<<"\n";//不能使用时间机器，直接输出
		return 0;
	}
	sort(a+1,a+n+1);//让祖先距离现在的时间从小到大排序
	 
	int l = 0,r = 0;//l,r表示当前间隔时间的起始点和终止点
	for(int i=1;i<=n;++i){
		while(l<=ans && r<=ans){//一直循环直到最远的那个时间机器点 
			if(a[i]>=l && a[i]<=r){//如果a[i]在 l,r 范围内 
				while(i<=n && a[i]<=r) i++;//找到第一个不在 l,r 范围内的祖先时间点 
				i--;//因为跳出循环后 i+1(for) , 所以要减掉 
				vec.push_back(r-l-12);//间隔时间 = r-l-12 (r当前在的时间机器是不能跳的)
				l = r;//左边界 = 右边界
				break;//进行下一次寻找 
			}else{//否则没有在范围内
				r += 12;//一直向更远古处寻找时间机器
			}
		}
	}
	
	sort(vec.begin(),vec.end(),greater<int>());//让间隔时间从大到小排序 
	for(int i=0;i<k;++i){//可以跳 k 次
		ans -= vec[i];//直接减掉最大的间隔时间 
	}
	cout<<ans<<"\n";//输出 
	return 0;
}
```






## 后记
特别鸣谢 @[ljx](https://www.luogu.com.cn/user/986551) 

UPD : $2024.9.4 ~ 10:07$ 

$\quad$ 初步完工 ( $A,B,D$ ) 题

UPD : $2024.9.4 ~ 19:51$

​	已写完 $C$ 题题解
