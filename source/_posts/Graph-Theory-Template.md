---
title: Graph Theory Template
top: true
cover: false
toc: true
mathjax: false
date: 2019-09-27 00:12:59
img: https://i.loli.net/2019/12/27/2HxiPN5M7Bu9Efo.jpg
password:
summary: 关于图论的一些模板代码
tags:
- template
- Graph Theory
- Algorithm
categories:
- Algorithm
---
## 拓扑排序
```cpp
  queue<int> Q;
  for(int i = 1; i <= n; i++)
    if(d[i] == 0) {Q.push(i);cmp[i] = 1;}
  while(!Q.empty())
  {
    int v = Q.front();
    Q.pop();
    for(int i = 0; i < G[v].size(); i++)
      {
        d[G[v][i]]--;
        if(!d[G[v][i]])
        {
          cmp[G[v][i]] = cmp[v]+1;
          Q.push(G[v][i]);
        }
      }
  }
```

## 2-sat & 强联通分量分解

### kosaraju

两遍 dfs  kosaraju 比tarjan慢

```cpp
//vector存边比较慢，可以考虑用链式前向星
int n,m;
vector <int> G[maxn];//正向邻接表
vector <int> rG[maxn];//反向邻接表
vector <int> vs;     //拓扑序存点
int cmp[maxn]; //标号
bool used[maxn];

void dfs(int x)
{
  used[x] = true;
  for(int i = 0; i < G[x].size(); i++)
   if(!used[G[x][i]])  dfs(G[x][i]);
  vs.push_back(x);
}

void rdfs(int x, int k)
{
  used[x] = true;
  cmp[x] = k;
  for(int i = 0; i < rG[x].size(); i++)
    if(!used[rG[x][i]]) rdfs(rG[x][i],k);
}

int scc()
{
  memset(used, false , sizeof(used));
  vs.clear();
  for(int i = 1; i <= n; i++)
     if(!used[i]) dfs(i);
  memset(used, false ,sizeof(used));
  int k = 0;
  for(int i = vs.size()-1; i >= 0; i--)
     if(!used[vs[i]])  rdfs(vs[i],++k);
  return k;
}
```

### Tarjan

```cpp
int n,m,tot,top,cnt,cnts;
// top 栈顶 cnts 强连通分量数量 cnt 时间戳
int sta[maxn],h[maxn],dfn[maxn],low[maxn],cmp[maxn];
//sta[] 堆栈， h[] 链式前向星  dfn[]dfs次序数组 cmp[]强连通分量标号
//low[] u结点或者u的子树结点所能追溯到的最早栈中结点的次序号
bool ins[maxn];
//判断是否在栈中

struct edge{
  int to,next;
  edge(int x = 0, int y = 0) : to(x), next(y) {}
}es[maxm];

void add_edge(int u,int v)
{
  es[tot] = edge(v,h[u]);
  h[u] = tot++;
}

init()
{
  tot = top = cnts = cnt = 0;
  memset(ins,false,sizeof(ins));
  memset(h,-1,sizeof(h));
  memset(dfn,0,sizeof(dfn));
}
void Tarjan(int x)
{
  dfn[x] = low[x] = ++cnt;
  ins[x] = true;
  sta[top++] = x;
  for(int i = h[x];  ~i ; i = es[i].next)
  {
    int v = es[i].to;
    if(!dfn[v])
    {
      Tarjan(v);
      if(low[x] > low[v])  low[x] = low[v];
    }
    else if(ins[v] && dfn[v] < low[x])
     low[x] = dfn[v];
  }
  if(dfn[x] == low[x])
  {
    cnts++;
    int t;
    do{
      t = sta[--top];
      ins[t] = false;
      cmp[t] = cnts;
    }while(t != x);
  }
}

void scc()
{
  for(int i = 1; i <= n; i++)
    if(!dfn[i]) Tarjan(i);
}
```



### 有序输出

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxn = 8000 * 2 + 100;
vector <int> G[maxn];
bool used[maxn];
int n,m,top;
int sta[maxn];

bool dfs(int x)
{
	if(used[x^1]) return false;
	if(used[x])   return true;
	used[x] = true;
	sta[top++] = x;
	for(int i = 0; i < G[x].size(); i++)
	  if(!dfs(G[x][i])) return false;
	return true;
}

bool judge()
{
	memset(used,false,sizeof(used));
	for(int i = 0; i < 2*n; i++)
	{
		if(!used[i] && !used[i^1])
		{
			top = 0;
			if(!dfs(i))
			{
				for(int j = 0; j < top; j++)
				   used[sta[j]] = false;
				top = 0;
				if(!dfs(i^1)) return false;
			}
		}
	}
	return true;
}

void solve()
{
  if(judge())
    {
      for(int i = 0; i < 2*n; i++)
      if(used[i]) printf("%d\n",i+1);
    }
  else puts("NIE");
}
```


## LCA

### 倍增

```cpp
int fa[maxn][32];
int depth[maxn];
//depth[root] = 1, dfs(root)
void dfs(int x)
{

    for(int i = 1; i <= 18; i++)
    {
        if(depth[x] < (1<<(i-1))) break;
        fa[x][i] = fa[fa[x][i-1]][i-1];
    }
    for(int i = h[x]; ~i; i = es[i].next)
    {
      int v = es[i].to;
      if(depth[v] >= 0) continue;
       fa[v][0] = x;
       depth[v] = depth[x] + 1;
       dfs(v);
    }
}

int lca(int x, int y)
{
    if(depth[x] < depth[y]) swap(x,y);
    int d = depth[x] - depth[y];
    for(int i = 0; i <= 18; i++)
        if(d & (1<<i)) x = fa[x][i];
    if(x == y) return x;
    for(int i = 18; i >= 0; i--)
        if(fa[x][i] != fa[y][i]) x = fa[x][i], y = fa[y][i];
    return fa[x][0];
}
```

## 求树的直径

+ 两遍dfs

```cpp
//Codeforces 592 D. Super M

#include <bits/stdc++/h>

#define r(a) scanf("%d",&a)
#define p(a) printf("%d",a)
const int INF = 0x3f3f3f3f;
const int maxn = 123456 + 100;
using namespace std;

vector <int> g[maxn];
int a[maxn];
int b[maxn];
bool vis[maxn];
set <int> cnt;
int st;
int ed;
int len_max;

void dfs_1(int x,int num)
{
    int rt = 0;
    for(int i = 0; i < g[x].size();i++)
    {
        int t = g[x][i];
        if(cnt.count(t) == 0 || vis[t]) continue;
                vis[t] = true;
        dfs_1(t,num+1);
                rt++;
    }
    if(rt == 0 && num >= len_max)
    {
        if(len_max == num) st = min(st,x);
        else st = x;
            len_max = num;
    }
}

bool dfs(int f,int x)
{
    bool rt = false;
    for(int i = 0; i < g[x].size(); i++)
    {
        int t = g[x][i];
        if(t == f) continue;
        if( dfs(x,t)||cnt.count(t)) vis[t] = rt = true;
    }
//  if(rt) cout << "##" << x<<endl;
    return rt;
}

void get_tree()
{
  memset(vis,false,sizeof(vis));
  vis[a[0]] = true;
 // cout << "##"<<a[0]<<endl;
    dfs(0,a[0]);
}

int main()
{
    int n,m;
        r(n); r(m);
    for(int i = 1; i < n; i++)
    {
        int v,u;
        r(v); r(u);
        g[v].push_back(u);
        g[u].push_back(v);
    }
        for(int i = 0; i < m; i++)
    {  r(a[i]);  cnt.insert(a[i]);}

    get_tree();
    int len = 0;
    for(int i = 1; i <= n; i++)
           if(vis[i]) {b[++len] = i; cnt.insert(i);}
//  cout << endl;

       memset(vis,false,sizeof(vis));
       st = INF;
       vis[b[1]] = true;
       len_max = 0;
      dfs_1(b[1],1);

       memset(vis,false,sizeof(vis));
       ed = st;
       st = INF;
       vis[ed] = true;
       len_max = 0;
       dfs_1(ed,1);
       printf("%d %d\n",min(st,ed),2*(len-1)-(len_max-1));
       return 0;
}
```



## 生成树

### 最小生成树

+ Kruskal / 并查集  O(ElgE)

```cpp
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e6 + 10;
const int maxm = 1e6 + 10;
const int INF = 0x3f3f3f3f;

int n,m;
int f[maxn];
struct edge{
    int u,v,c;
    edge(int u = 0, int v = 0, int c = 0) : u(u), v(v), c(c) {}
}es[maxm];

bool cmp(const edge &a,const edge &b)
{
     return a.c < b.c;
}

void init()
{
    for(int i = 0; i <= n; i++) f[i] = i;
}

int find(int x)
{
  return f[x] == x ? x : f[x] = find(f[x]);
}

void unit(int x,int y)
{
    x = find(x);
    y = find(y);
    f[x] = y;
}

bool same(int x,int y)
{
   return find(x) == find(y);
}

int main()
{
   scanf("%d%d",&n,&m);
   init();
   for(int i = 0; i < m; i++)
   {
     int u,v,c;
     scanf("%d%d%d",&u,&v,&c);
     es[i] = edge(u,v,c);
   }
   sort(es,es+m,cmp);
   int ans = 0,cnt = 0;
   for(int i = 0; i < m; i++)
   {
     if(!same(es[i].u,es[i].v))
     {
       unit(es[i].u,es[i].v);
       ans += es[i].c;
       if(++cnt == n-1) break;
     }
   }
   printf("%d\n",ans);
   return 0;
}

```

### 次小生成树

```cpp
//O(VElogE)
int n,m;
int g[maxn][maxn];
bool in[maxn][maxn];//标记其是否是最小生成树上的边
int md[maxn][maxn];//记录最小生成树的u-v之间的最大边权
//省略并查集模板
int vis[maxn];
//构造最大边权
void dfs(int s,int x, int dis)
{
   vis[x] = true;
   for(int i = 1; i <= n; i++)
    {
       if(g[x][i] != -1 && !vis[i] && in[x][i])
            {
             md[s][i] = max(dis,g[x][i]);
             dfs(s,i,max(dis,g[x][i]));
             }
    }
}

int Unique_MST()
{
  init();
  memset(g,-1,sizeof(g));
  memset(in,false,sizeof(in));
  for(int i = 0; i < m; i++)
   {
      int u,v,c;
      scanf("%d%d%d",&u,&v,&c);
      es[i] = edge(u,v,c);
      g[u][v] = g[v][u] = c;
   }
 sort(es,es+m,cmp);
 int res = 0, cnt = 0;
 for(int i = 0; i < m; i++)
  {
     if(!same(es[i].u,es[i].v))
       {
          res += es[i].c;
          unit(es[i].u,es[i].v);
          in[es[i].u][es[i].v] = in[es[i].v][es[i].u] = true;
          if(++cnt == n-1) break;
       }
  }
 for(int i = 1; i <= n; i++)
    {
       memset(vis,false,sizeof(vis));
       dfs(i,i,0);
    }
 //cout << res << endl;
 int ans = INF;
 for(int i = 0; i < m; i++)
       {
          if(in[es[i].u][es[i].v]) continue;
          ans = min(ans,res-md[es[i].u][es[i].v] + es[i].c);
          //cout << es[i].u << " " << es[i].v << " " << md[es[i].u][es[i].v] << endl;
       }
 return ans;
}
```

### 最小树形图

+ 朱刘算法 O(VM)

不定根最小树形图就再加一个超级源点，源点与所有节点连边，边权 = sum + 1(sum = c1 + ..cm)即可。
然后最后与原点相连的就是root.主要修改下面两个地方

```cpp
   if(es[i].c < in[v] && u != v)
       {
         in[v] = es[i].c,pre[v] = u;
         if(u == root) rt = i; //利用边m - m+n-1 与点1-n的映射关系
       }


    for(int i = 1; i <= n; i++)
        es[m+i-1] = edge(n+1,i,sum);
    type ans = MTG(n+1,n+1,m+n);
    if(ans >= 2*sum) printf("impossible\n\n");
    else printf("%lld %d\n\n",ans-sum,rt-m);
```

```cpp
const double eps = 1e-8;
#define type double
type in[maxn];
int id[maxn],used[maxn],pre[maxn];

struct edge
{
  int u,v;
  type c;
  edge(int x = 0, int y = 0, type z = 0) : u(x),v(y),c(z) {}
}es[maxm];

type MTG(int root, int n, int m)
{
  type res = 0;
  while(1)
  {
    for(int i = 1; i <= n; i++)  in[i] = INF,id[i] = used[i] = -1;
    for(int i = 0; i < m; i++)
     {
       int u = es[i].u;
       int v = es[i].v;
       if(es[i].c < in[v] && u != v)  in[v] = es[i].c,pre[v] = u;
     }//找最小的入边
    for(int i = 1; i <= n; i++)
     {
       if(i == root) continue;
       res += in[i];
       if(fabs(in[i] - INF) < eps) return -1;
     }//加入所有的入边权到答案中
    int cnt = 0;
    for(int i = 1; i <= n; i++)//枚举每个点为起点进行找环
    {
      int v = i;
      while(v != root && used[v] != i && id[v] == -1)  used[v] = i,v = pre[v];
      if(v != root && id[v] == -1)//找到环的时候进行缩点编号
       {
         ++cnt;
         id[v] = cnt;
         for(int u = pre[v]; u != v; u = pre[u]) id[u] = cnt;
       }
    }
    if(cnt == 0) break;//无环则退出
    for(int i = 1; i <= n; i++)
       if(id[i] == -1)  id[i] = ++cnt;

    for(int i = 0; i < m; i++)//更新边
     {
       int u = es[i].u;
       int v = es[i].v;
       es[i].u = id[u];
       es[i].v = id[v];
       if(u != v)  es[i].c -= in[v]; //更新边权
     }
     n = cnt;//更新节点数目和根节点编号
     root = id[root];
     }
     return res;
  }
```

### 生成树计数

+ 基尔霍夫矩阵树定理

无向图的基尔霍夫矩阵: 对角线上表示每个点的度数,若ij之间有边则矩阵ij处为-1
无向图的生成树的数目为: 任意一个n-1阶主子式的行列式的绝对值.


```cpp
#define type long long
int n,m;
int d[maxn];//记录度数
int g[maxn][maxn];//邻接矩阵
type c[maxn][maxn];//基尔霍夫矩阵

//行列式求和
type det(type a[][maxn], int n)
{  
  type ret=1;
  for(int i=1;i<=n;i++)
  {
    for(int j=i+1;j<=n;j++)
    {
      while(a[j][i])
      {
        type t=a[i][i]/a[j][i];
        for(int k=i;k<=n;k++)
            a[i][k]=a[i][k]-a[j][k] * t;

        for(int k=i;k<=n;k++)
            swap(a[i][k],a[j][k]);
        ret=-ret;
      }
    }
    if(a[i][i]==0)  return 0;
    ret=ret*a[i][i];
  }
  if(ret<0)   ret=-ret;
  return ret;
}

type num_st(int n, int m)
{
     for(int i = 1; i <= n; i++)
       for(int j = 1; j <= n; j++)
         {
           if(i == j) c[i][j] = d[i];
           else c[i][j] = -g[i][j];
         }

    // for(int i = 1; i <= n; i++)
    //  for(int j = 1; j <= n; j++)
    //     printf("%lld%c",c[i][j],j == n ? '\n' : ' ');

    return det(c,n-1);
}
```

## 单源最短路

### dijkstra

O(ElgV) 适合不含非负权边最短路

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long LL;
typedef pair <int,int> P;
const int maxn = 100+10;
const int INF = 0x3f3f3f3f;
int n,m;
int d[maxn];

struct edge
{
    int to,cost;
    edge(int u = 0,int d = 0) : to(u),cost(d) {}
};

vector <edge> G[maxn];

void dijkstra()
{
    memset(d,INF,sizeof(d));
    d[1] = 0;
    priority_queue < P,vector<P>,greater<P> >  Q;//P.first 是距离，.P.second 是顶点
    Q.push(P(0,1));
    while(!Q.empty())
    {
        P x = Q.top();
        Q.pop();
        int dist = x.first;
        int u = x.second;

        if(d[u] < dist) continue;//排除u相同时, dist不同的情况。
        for(int i = 0; i < G[u].size(); i++)
        {
            edge e = G[u][i];
            if(d[e.to] > d[u] + e.cost)
            {
                d[e.to] = d[u]+ e.cost;
                Q.push(P(d[e.to],e.to));
            }
        }
    }
    return ;
}

void input()
{
    for(int i = 0; i <= n; i++ )
        G[i].clear();
    for(int i = 0; i < m; i++)
    {
      int u,v,dist;
      scanf("%d%d%d",&u,&v,&dist);
      G[u].push_back(edge(v,dist));
      G[v].push_back(edge(u,dist));
    }
}

int main()
{
    while(scanf("%d%d",&n,&m) && n && m)
    {
        input();
        dijkstra();
        printf("%d\n",d[n]);
     }
    return 0;
}
```

### spfa  

+ bfs O(KE - VE)

```cpp
bool spfa()
{
    memset(d,INF,sizeof(d));
    memset(cnt,0,sizeof(cnt));
    memset(inq,false,sizeof(inq));
    queue <int> Q;
    Q.push(1);
    d[1] = 0;
    cnt[1] = 1;
    inq[1] = true;
    while(!Q.empty())
    {
        int t = Q.front();
        Q.pop();
        inq[t] = false;
        for(int i = 0; i < G[t].size(); i++)
        {
            edge e = G[t][i];
            if(d[e.to] > d[t] + e.cost)
            {
                d[e.to] = d[t] + e.cost;
                if(!inq[e.to]) {
                    if(++cnt[e.to] > n)  return false;
                    Q.push(e.to);
                    inq[e.to] = true;
                }
            }
        }
    }
    return true;
}

```

+ dfs + spfa 找负环 O(KM)

```cpp
//  memset(vis,false,sizeof(vis));
//  memset(d,INF,sizeof(d));
//   d[0] = 0;
int d[maxn];
bool vis[maxn];

bool spfa(int u)
{
  vis[u] = true;
  for(int i = h[u]; ~i ; i = es[i].next)
  {
    int v = es[i].to;
    if(d[v] > d[u] + es[i].c)
    {
      d[v] = d[u] + es[i].c;
      if(vis[v]) return false;
      if(!spfa(v)) return false;
    }
  }
  vis[u] = false;
  return true;
}
```


### Bellman_Ford  O(VE)

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long LL;
typedef pair <int,int> P;
const int maxn = 100+10;
const int maxm = 1e4 + 100;
const int INF = 0x3f3f3f3f;
int n,m;
int d[maxn];

struct edge {
  int from,to,cost;
};

edge es[maxm*2];

void Bellman_Ford()
{
    memset(d,INF,sizeof(d));
    d[1] = 0;
    for(int i = 1; i < n; i++)
    {
        for(int j = 1; j <= 2*m; j++)
        {
            edge e = es[j];
            if(d[e.from] < INF && d[e.to] > d[e.from] + e.cost)  d[e.to] = d[e.from] + e.cost;
        }
    }
}

int main()
{
    while(scanf("%d%d",&n,&m)&& n && m)
    {
        int j = 1;
        for(int i = 1; i <= m; i++)
        {
            int u,v,dist;
            scanf("%d%d%d",&u,&v,&dist);
            edge e1,e2;
            e1.from = u,e1.to = v,e1.cost = dist;
            e2.from = v,e2.to = u,e2.cost = dist;
            es[j++] = e1;
            es[j++] = e2;
        }
        Bellman_Ford();
        printf("%d\n",d[n]);
    }
}
```

## 多源最短路

### Floyd

+ O(V^3)

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long LL;
typedef pair <int,int> P;
const int maxn = 100+10;
const int maxm = 1e4 + 100;
const int INF = 0x3f3f3f3f;
int n,m;
int mp[maxn][maxn];

void Floyd()
{
    for(int k = 1; k <= n; k++)
        for(int i = 1; i <= n; i++)
          for(int j = 1; j <= n; j++)
               mp[i][j] = min(mp[i][j],mp[i][k]+mp[k][j]);
}

int main()
{
    while(scanf("%d%d",&n,&m)&& n && m)
    {
        memset(mp,INF,sizeof(mp));
        int j = 1;
        for(int i = 1; i <= m; i++)
        {
            int u,v,dist;
            scanf("%d%d%d",&u,&v,&dist);
            mp[u][v] = dist;
            mp[v][u] = dist;
        }
        Floyd();
        printf("%d\n",mp[1][n]);
    }
    return 0;
}
```

## 网络流

### 最大流

#### EK

```cpp
//Max_flow
//@2018/05/02 Wednesday
//EK algorithm [Edmonds Karp] O(V*E^2) O(v^2)
//by Tawn
#include <bits/stdc++.h>

using namespace std;
const int maxn = 1e3;
const int INF = 0x3f3f3f3f;

int n,m; //n - Vertices  m - edges
int pre[maxn]; //record predecesor and sign if it is visited
int cap[maxn][maxn]; //record the capacity of residual network
int flow[maxn]; //record the residual flow from starting vertex to current vertex
queue <int> q;

int bfs(int st, int ed)
{
  memset(pre,-1,sizeof(pre));
  while(!q.empty()) q.pop();
  pre[st] = 0;
  flow[st] = INF;
  q.push(st);
  while(!q.empty())
  {
      int t = q.front();
      q.pop();
      if(t == ed) break;
      for(int i = 1; i <= n; i++)
      {
          if(pre[i] == -1 && cap[t][i] > 0)
          {
              pre[i] = t;
              flow[i] = min(flow[t],cap[t][i]);
              q.push(i);
          }
      }
  }
  if(pre[ed] == -1)  return -1;
  else              return flow[ed];
}


int EK(int st, int ed)
{
    int res = 0; //the augmenting flow
    int sum = 0; //the max_flow
    while((res = bfs(st,ed)) != -1)//argumenting path
    {
        int k = ed;
        while(k != st)
        {
            int f = pre[k];
            cap[f][k] -= res;
            cap[k][f] += res;//reversible edge
            k = f;
        }
        sum += res;
    }
    return sum;
}
int main()
{
   int s,t,c;
   scanf("%d%d",&n,&m);
   memset(cap,0,sizeof(cap));
   for(int i = 0; i < m; i++)
   {
       scanf("%d%d%d",&s,&t,&c);
       cap[s][t] = c;
   }
   int ans = EK(1,n);
   printf("%d\n",ans);
   return 0;
}
```

#### Dinic

```cpp
//Max_flow
//@2018/05/04 Friday
//Dinic  O(n^2 * m)  O(m*3*2)
//by Tawn
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <queue>
#include <cstring>

using namespace std;
const int INF = 0x3f3f3f3f;
const int maxn = 1000 + 10;
const int maxm = 1e6 + 10;

int n,m;
int l[maxn];//记录层数
int h[maxn];//链式前向星
int cur[maxn];
int tot = 0;

struct edge
{
  int to;
  int c;
  int next;
  edge(int x = 0, int y = 0, int z = 0) : to(x), c(y), next(z) {}
 }es[maxm*2];//记录边 注意是2倍

void add_edge(int u, int v, int c)
{
    es[tot] = edge(v,c,h[u]);
    h[u] = tot++;
    es[tot] = edge(u,0,h[v]);
    h[v] = tot++;
}

bool bfs(int s, int t)
{
   memset(l,0,sizeof(l));
   l[s] = 1;
   queue <int> q;
   q.push(s);
   while(!q.empty())
   {
    int u = q.front();
    q.pop();
    if(u == t)  return true;
    for(int i = h[u]; i != -1; i = es[i].next)
        {
         int v = es[i].to;
         if(!l[v] && es[i].c) {l[v] = l[u] + 1; q.push(v);}
        }
   }
   return false;
}

int dfs(int x, int t, int mf)
{
    if(x == t) return mf;
    int ret = 0;
    for(int &i = cur[x]; i != -1; i = es[i].next)
    {
      if(es[i].c && l[x] == l[es[i].to] - 1)
      {
        int f = dfs(es[i].to,t,min(es[i].c,mf - ret));
        es[i].c -= f;
        es[i^1].c += f;
        ret += f;
        if(ret == mf) return ret;
      }
    }
    return ret;
}

int dinic(int s, int t)
{
  int ans = 0;
  while(bfs(s,t))
  {
   for(int i = 0; i <= t; i++) cur[i] = h[i];
   ans += dfs(s,t,INF);
   }
  return ans;
}


int main()
{
   while(~scanf("%d%d",&n,&m))
   {
   tot = 0;
   memset(h,-1,sizeof(h));
   int u,v,c;
   for(int i = 0; i < m; i++)
   {
    scanf("%d%d%d",&u,&v,&c);
    add_edge(u,v,c);
   }
   int ans = dinic(1,n);
   printf("%d\n",ans);
   }
   return 0;   
}
```

#### SAP

```cpp
//Max_flow
//@2018/05/04 Friday
//SAP  O(n^2 * m)  O(m*3*2)
//by Tawn
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <queue>
#include <cstring>

using namespace std;
const int INF = 0x3f3f3f3f;
const int maxn = 200 + 10;
const int maxm = 200 + 10;

int n,m;
int head[maxn];//链式前向星
int tot = 0;

struct edge
{
 int to;
 int c;
 int next;
 edge(int x = 0, int y = 0, int z = 0) : to(x), c(y), next(z) {}
}es[maxm*2];//记录边 注意是2倍

void add_edge(int u, int v, int c)
{
   es[tot] = edge(v,c,head[u]);
   head[u] = tot++;
}


int SAP(int s, int t)
{
   int numh[maxn],h[maxn],ce[maxn],pre[maxn];
   //numh 记录gap优化的统计高度数量数组，h 距离标号数组，ce 当前弧，pre前驱数组
   int f, ans = 0, u, temp, neck, i; //初始化最大流为0
   memset(h,0,sizeof(h));
   memset(numh,0,sizeof(numh));
   memset(pre,-1,sizeof(pre));
   for(i = 1; i <= n; i++)  ce[i] = head[i];
   numh[0] = n;
   u = s;
   while(h[s] < n)
   {
       //寻找增广路
       if(u == t)
       {
           f = INF;
           for(i = s; i != t; i = es[ce[i]].to)
           {
               if(f > es[ce[i]].c)
               {
                   neck = i;
                   f = es[ce[i]].c;
               }
           }
           for(i = s; i != t; i = es[ce[i]].to)
           {
               temp = ce[i];
               es[temp].c -= f;
               es[temp^1].c += f;
           }
           ans += f;
           u = neck;
       }

       //寻找可行弧
       for(i = ce[u]; i != -1; i = es[i].next)
           if(es[i].c && h[u] == h[es[i].to] + 1)  break;

      //寻找增广路
       if(i != -1)
       {
           ce[u] = i;
           pre[es[i].to] = u;
           u = es[i].to;
       }
       else
       {
           if(!--numh[h[u]]) break; //gap optimization
           ce[u] = head[u];
           for(temp = n, i = head[u]; i != -1; i = es[i].next)
               if(es[i].c)  temp = min(temp, h[es[i].to]);

           h[u] = temp + 1;
           ++numh[h[u]];
           if(u != s) u = pre[u];//重标号并且从当前点前驱重新增广
       }

   }
   return ans;
}

int main()
{
  while(~scanf("%d%d",&n,&m))
  {
  tot = 0;
  memset(head,-1,sizeof(head));
  int u,v,c;
  for(int i = 0; i < m; i++)
  {
   scanf("%d%d%d",&u,&v,&c);
   add_edge(u,v,c);
   add_edge(v,u,0);//增加反向边
  }
  int ans = SAP(1,n);
  printf("%d\n",ans);
  }
  return 0;   
}
```

### 最小费用最大流

#### EK & Spfa  

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxn = 100  + 100;
const int maxm = 100 + 100;
const int INF = 0x3f3f3f3f;
typedef long long LL;
typedef pair<int,int> P;
//const LL mod = 1e9 + 7;

#define PI 3.1415926
#define sc(x)  scanf("%d",&x)
#define pf(x)  printf("%d",x)
#define pfn(x) printf("%d\n",x)
#define pfln(x) printf("%lld\n",x)
#define pfs(x) printf("%d ",x)
#define rep(n) for(int i = 0; i < n; i++)
#define per(n) for(int i = n-1; i >= 0; i--)
#define mem(a,x) memset(a,x,sizeof(a))

struct edge
{
  int from,to,cap,flow,cost;
  edge(int from,int to, int cap, int flow, int cost) : from(from),to(to),cap(cap),flow(flow),cost(cost) {}
};

int tot = 0;
vector<edge> E;
vector<int> G[maxn];
int inq[maxn];
int d[maxn];
int p[maxn];
int a[maxn];


void addedge(int from, int to, int cap, int cost)
{
  E.push_back(edge(from,to,cap,0,cost));
  E.push_back(edge(to,from,0,0,-cost));
  tot = E.size();
  G[from].push_back(tot-2);
  G[to].push_back(tot-1);
}

bool spfa(int s, int t, int &flow, int &cost)
{
  mem(d,INF);
  mem(inq,0);
  d[s] = 0,inq[s] = 1; p[s] = 0, a[s] = INF;
  queue<int> Q;
  Q.push(s);
  while(!Q.empty())
  {
    int u = Q.front();Q.pop();
    inq[u] = 0;
    for(int i = 0; i < G[u].size(); i++)
    {
      edge e = E[G[u][i]];
      if(e.cap > e.flow && d[e.to] > d[u] + e.cost)
      {
        d[e.to] = d[u] + e.cost;
        p[e.to] = G[u][i];
        a[e.to] = min(a[u],e.cap - e.flow);
        if(!inq[e.to]) {Q.push(e.to); inq[e.to] = 1;}
      }
    }
  }

  if(d[t] == INF) return false;
  flow += a[t];
  cost += d[t] * a[t];
  int u = t;
  while(u != s)
  {
    E[p[u]].flow += a[t];
    E[p[u]^1].flow -= a[t];
    u = E[p[u]].from;
  }
  return true;
}

int mcf(int s, int t)
{
  int flow = 0,cost = 0;
  while(spfa(s,t,flow,cost)) ;
  return cost;
}

int main()
{
   //记得初始化各个变量
   int s,t;
   mcf(s,t);
}
```


### 上下界网络流

#### 无源汇可行流

```cpp
// 此处省略dinic板子

int B[maxm];
int in[maxn];

int main()
{

 int T;
 scanf("%d",&T);
 while(T--)
 {
   scanf("%d%d",&n,&m);
   tot = 0;
   memset(h,-1,sizeof(h));
   memset(in,0,sizeof(in));
   for(int i = 0; i < m; i++)
   {
      int u,v,b,c;
      scanf("%d%d%d%d",&u,&v,&b,&c);
      add_edge(u,v,c-b);
      B[i] = b;
      in[u] -= b;
      in[v] += b;
   }
  int s = 0, t = n+1;
  int res = 0;
  for(int i = 1; i <= n; i++)
  {
     if(in[i] > 0) {res += in[i]; add_edge(s,i,in[i]);}
     if(in[i] < 0) {add_edge(i,t,-in[i]);}
  }
  int ans = dinic(s,t);
  if(ans != res) printf("NO\n");
  else {
	  printf("YES\n");
	  for(int i = 0; i < m; i++)
	  {
              printf("%d\n",B[i] + es[2*i+1].c);
	  }
    }
 }
 return 0;
}
```

#### 有源汇可行流

新增超级源点超级汇点  &&  t -INF-> s

```cpp
//2018/10/04
//by Tawn in JNU
// 此处省略dinic板子
int B[maxn][30];
int C[maxn][30];
int in[maxn];

int main()
{
 //freopen("in.txt","r",stdin);
 int T;
 scanf("%d",&T);
 while(T--)
 {
   scanf("%d%d",&n,&m);
   int s = n+m+1, t = n+m+2,ss = 0,tt=t+1;
   tot = 0;
   memset(h,-1,sizeof(h));
   memset(in,0,sizeof(in));

  add_edge(t,s,INF);
  int res = 0;  
  for(int i = 1; i <= t; i++)
   {
   if(in[i] > 0)  {add_edge(ss,i,in[i]);res += in[i];}
   if(in[i] < 0)  add_edge(i,tt,-in[i]);
   }

  int ans = dinic(ss,tt);
  if(ans != res) printf("IMPOSSIBLE\n");
  else {
         int k = 1;
	 for(int i = 1; i <= n; i++)
	   for(int j = 1; j <= m; j++,k+=2)
		 printf("%d%c",B[i][j]+es[k].c, j == m? '\n':' ');
    }
 printf("\n");
 }
 return 0;
}

```

#### 有源汇最大流

```cpp
// 此处省略dinic板子

int in[maxn];
int g[maxn];
int d[maxn];
int b[maxm*2];//该边下界

int main()
{
   while(~scanf("%d%d",&n,&m))
  {
   memset(h,-1,sizeof(h));
   memset(in,0,sizeof(in));
   tot = 0;
   int s = n+m+1,t = n+m+2,ss = 0,tt = t+1,totc = 0;

   add_edge(t,s,INF);

   int res = 0;
   for(int i = 1; i <= t; i++)
   {
      if(in[i] > 0) {add_edge(ss,i,in[i]); res += in[i];}
      if(in[i] < 0) add_edge(i,tt,-in[i]);
   }
   int ans = dinic(ss,tt);
   if(ans == res)
   {
      add_edge(t,s,-INF);
      int _ans = dinic(s,t);
      printf("%d\n",_ans);
      for(int i = 1; i < totc; i += 2)
        printf("%d\n",es[i].c + b[i]);
   }
   else printf("-1\n");
   printf("\n");
  }
   return 0;
}               
```

#### 有源汇最小流

```cpp
// 此处省略dinic板子

int in[maxn];

int main()
{
  while(~scanf("%d%d",&n,&m) && (n||m))
  {
    tot = 0;
    memset(h,-1,sizeof(h));
    memset(in,0,sizeof(in));
    int s = n+1, t = n+2, ss = 0, tt = t+1;
    for(int i = 0; i < m; i++)
    {
      string s1,s2;
      int u,v,p;
      scanf("%d%d%d",&u,&v,&p);
      in[u] -= p;
      in[v] += p;
      add_edge(u,v,INF);
    }
    int res = 0;
    for(int i = 1; i <= t; i++)
    {
      if(in[i] > 0) {res += in[i]; add_edge(ss,i,in[i]);}
      if(in[i] < 0)  add_edge(i,tt,-in[i]);
    }
    int ans = dinic(ss,tt);
    add_edge(t,s,INF);
    ans += dinic(ss,tt);
    if(res == ans)  printf("%d\n",es[h[t]^1].c);
    else printf("impossible\n");
  }
  return 0;
}
```

## 二分图

### 判断是否二分图

```cpp
bool g[maxn][maxn];
int col[maxn];
//利用0，1染色，层序遍历，用同色则为false
bool bfs(int n)
{
    memset(col,-1,sizeof(col));
    for(int i = 0; i < n; i++)
    {
        if(col[i] != -1) continue;
        queue<int> q;
        col[i] = 1;
        q.push(i);
        while(!q.empty())
        {
            int from = q.front();
            q.pop();
            for(int to = 0; to < n; to++)
            {
                if(g[from][to] && col[to] == -1)
                {
                    col[to] = !col[from];
                    q.push(to);
                }
                if(g[from][to] && col[to] == col[from])
                    return false;
            }
        }
    }
    return true;
}
```

### 二分图匹配

+ 匈牙利算法

```cpp
bool g[maxn][maxn];
bool vis[maxn];
int line[maxn];
//寻找增广路O(VE)
bool dfs(int u,int n)
{
    for(int v = 0; v < n; v++)
    {
        if(g[u][v] && !vis[v])
        {
        vis[v] = true;
        if(line[v] == -1 || dfs(line[v],n))
        {line[v] = u;return true;}
        }
    }
    return false;
}

int Max_match(int n,int p)
{
    int all = 0;
    memset(line,-1,sizeof(line));
    for(int i = 0; i < p; i++)
    {
        memset(vis,false,sizeof(vis));
        if(dfs(i,n)) all++;
    }
    return all;
}
```

+ Hopcroft-Carp

```cpp
// O(sqrt(n)*e)
typedef long long LL;
using namespace std;
const int maxn = 3010;
const int INF = 0x3f3f3f3f;
bool g[maxn][maxn];
bool vis[maxn];
int dx[maxn],dy[maxn];
int Mx[maxn],My[maxn];
int dis;
int GM[maxn][2];
int GN[maxn][2];
int speed[maxn];
double _time;
int M,N;

bool bfs(int n,int m)
{
    dis = INF;
    memset(dx,-1,sizeof(dx));
    memset(dy,-1,sizeof(dy));
    queue <int> que;
    for(int i = 0; i < n; i++)
    {
        if(Mx[i] == -1)
            {
                que.push(i);
                dx[i] = 0;
            }
    }

    while(!que.empty())
    {
        int u = que.front();
        que.pop();
        if(dx[u] > dis)  break;
        for(int v = 0; v < m; v ++)
        {
            if(g[u][v] && dy[v] == -1)
            {
                dy[v] = dx[u] + 1;
                if(My[v] == -1) dis = dy[v];
                else {
                    dx[My[v]] = dy[v] + 1;
                    que.push(My[v]);
                }
            }
        }
    }
    return dis != INF;
}


bool dfs(int u,int m)
{
   for(int v = 0; v < m; v++)
   {
      if(g[u][v] && !vis[v] && dy[v] == dx[u] + 1)
      {
          vis[v] = true;
          if(My[v] != -1 && dy[v] == dis) continue;

          if(My[v] == -1 || dfs(My[v],m))
          {
              My[v] = u;
              Mx[u] = v;
              return true;
          }
      }
   }
    return false;
}



int Max_match(int n,int m)
{
    int all = 0;
    memset(Mx,-1,sizeof(Mx));
    memset(My,-1,sizeof(My));
    while(bfs(n,m))
    {
       memset(vis,false,sizeof(vis));
       for(int i = 0; i < n; i++)
            if(Mx[i] == -1 && dfs(i,m))
            all++;
    }
    return all;
}
```

### 最小路径覆盖

+ 最小路径覆盖 = n-二分图最大匹配数  拆点建二分图

```cpp
/*
     int f = dfs(es[i].to,t,min(es[i].c,mf - ret));
        if(f) nxt[x] = es[i].to;
*/

int nxt[maxn];//记录路径
int vis[maxn];//查找路径时标记点

void solve()
{
   scanf("%d%d",&n,&m);
   tot = 0;
   memset(nxt,-1,sizeof(nxt));
   memset(h,-1,sizeof(h));
   memset(vis,false,sizeof(vis));

   int s = 0, t = 2*n+1,u,v;
   for(int i = 1; i <= n; i++)
     add_edge(s,i,1),add_edge(i+n,t,1);

   for(int i = 0; i < m; i++)
   {
       scanf("%d%d",&u,&v);
       add_edge(u,v+n,1);
   }

   int res = dinic(s,t);

   printf("%d\n",n-res); //最小路径覆盖 = n - 二分图最大匹配
   for(int i = 1; i <= n; i++)
       if(!vis[i])
       {
           vector <int> v;
           int t = i;
           do{
            vis[t] = true;
            v.push_back(t);
            t = nxt[t]-n;
           }while(t > 0);
           for(int j = 0; j < v.size(); j++)
             printf("%d%c",v[j], j == v.size() -1 ? '\n' : ' ');
       }
}
```

### 补充

二分图最小点权覆盖集 = 最小割 = 最大流，  最大点权独立集 = 总权 - 最小点权覆盖集
