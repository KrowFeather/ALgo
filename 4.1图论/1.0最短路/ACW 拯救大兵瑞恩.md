# 知识点
  ## [[状态压缩]] [[单源最短路问题]]
# 题目
 1944 年，特种兵麦克接到国防部的命令，要求立即赶赴太平洋上的一个孤岛，营救被敌军俘虏的大兵瑞恩。

瑞恩被关押在一个迷宫里，迷宫地形复杂，但幸好麦克得到了迷宫的地形图。

迷宫的外形是一个长方形，其南北方向被划分为 N 行，东西方向被划分为 M 列，于是整个迷宫被划分为 N×M 个单元。

每一个单元的位置可用一个有序数对 (单元的行号, 单元的列号) 来表示。

南北或东西方向相邻的 2 个单元之间可能互通，也可能有一扇锁着的门，或者是一堵不可逾越的墙。

注意： 门可以从两个方向穿过，即可以看成一条无向边。

迷宫中有一些单元存放着钥匙，同一个单元可能存放多把钥匙，并且所有的门被分成 P 类，打开同一类的门的钥匙相同，不同类门的钥匙不同。

大兵瑞恩被关押在迷宫的东南角，即 (N, M) 单元里，并已经昏迷。

迷宫只有一个入口，在西北角。

也就是说，麦克可以直接进入 (1,1) 单元。

另外，麦克从一个单元移动到另一个相邻单元的时间为 1，拿取所在单元的钥匙的时间以及用钥匙开门的时间可忽略不计。

试设计一个算法，帮助麦克以最快的方式到达瑞恩所在单元，营救大兵瑞恩。

## 输入格式
第一行有三个整数, 分别表示 N, M, P 的值。

第二行是一个整数 k，表示迷宫中门和墙的总数。

接下来 k 行，每行包含五个整数，Xi 1, Yi 1, Xi 2, Yi 2, Gi
：当 Gi≥1 时，表示 (Xi 1, Yi 1) 单元与 (Xi 2, Yi 2) 单元之间有一扇第 Gi
 类的门
 当 Gi=0 时，表示 (Xi 1, Yi 1) 单元与 (Xi 2, Yi 2) 单元之间有一面不可逾越的墙。

接下来一行，包含一个整数 S，表示迷宫中存放的钥匙的总数。

接下来 S 行，每行包含三个整数 Xi 1, Yi 1, Qi，表示 (Xi 1, Yi 1) 单元里存在一个能开启第 Qi 类门的钥匙。

## 输出格式
输出麦克营救到大兵瑞恩的最短时间。

如果问题无解，则输出 -1。

## 数据范围
|Xi 1−Xi 2|+|Yi 1−Yi 2|=1
,
0≤Gi≤P
,
1≤Qi≤P
,
1≤N, M, P≤10
,
1≤k≤150
## 输入样例 ：
4 4 9
9
1 2 1 3 2
1 2 2 2 0
2 1 2 2 0
2 1 3 1 0 
2 3 3 3 0
2 4 3 4 1
3 2 3 3 0
3 3 4 3 0
4 3 4 4 0
2
2 1 2 
4 2 1
## 输出样例 ：
14

![[Pasted image 20230523182613.png]]
# 思路
·我们先不考虑任何输入输出，只考虑怎么通过给定的图去解决问题，我们首先发现，我们需要一个变量用于表示我们现有的钥匙的状态，那么我们不妨用一个二进制数来表示，按照每一个二进制位上是否位 1 来判断是不是可以。
·之后我们开始找最短路，我们走最短路其实就是用前一步的状态去更新后一步的状态，我们这里的状态可以表示我们拿着 m 状态的钥匙下，走到 k 点我们所花费的最小步数。显然，我们有转移方程 $dist[i][k]=min(dist[i-1][k],dist[i][k]$
另外，捡到钥匙我们可以通过判断某一个格子上是否有钥匙，有钥匙就捡起来，然后把新状态丢到原来的格子里面。
·判断能不能通过门，我们只需要判断我们手上的钥匙是否包含了我们开门所需要的钥匙。
# AC 代码
```cpp
#include <iostream>
#include <queue>
#include <cstring>
using namespace std;
const int N = 105,M = 12;
typedef pair<int,int> PII;
int g[N][N],key[N],d[N][1<<M];
bool st[N][1<<M];
int n,m,p,k,s;
int dx[] = {0,1,0,-1};
int dy[] = {1,0,-1,0};
queue<PII> q;
int get(int x,int y){
    return (x - 1) * m + y;
}
int bfs(){
    int t = get(1,1);
    q.push({t,key[t]});
    st[t][key[t]] = true;
    memset(d,0x3f,sizeof d);
    d[t][key[t]] = 0;
    while(q.size()){
        PII u = q.front();
        q.pop();
        int z = u.first,v = u.second;
        for(int i = 0;i < 4;i++){
            Int x = (z - 1) / m + dx[i] + 1, y = (z - 1) % m + dy[i] + 1;
            int v1 = v,z1 = get(x,y);
            if(!x || !y || x > n || y > m || !g[z][z1]) continue;
            if(g[z][z1] != -1){
                if(!(v >> g[z][z1] & 1))    continue;
            }
            v1 |= key[z1];
            if(!st[z1][v1]){
                q.push({z1,v1});
                st[z1][v1] = true;
                d[z1][v1] = d[z][v] + 1;
            } 
            if(z1 == n * m) return d[z1][v1];
        }
    }
    return -1;
}
int main(){
    cin>>n>>m>>p;
    cin>>k;
    int x1,y1,x2,y2,z,z1,z2;
    memset(g,-1,sizeof g);
    while(k--){
        cin>>x1>>y1>>x2>>y2>>z;
        z1 = get(x1,y1),z2 = get(x2,y2);
        g[z1][z2] = g[z2][z1] = z;
    }
    cin>>s;
    while(s--){
        cin>>x1>>y1>>z;
        key[get(x1,y1)] |= 1 << z;
    }
    cout<<bfs()<<endl;
    return 0;
}
```
# 备注
