# PAT A1030-Travel Plan（Dijkstra）

## 题目

A traveler's map gives the distances between cities along the highways, together with the cost of each highway. Now you are supposed to write a program to help a traveler to decide the shortest path between his/her starting city and the destination. If such a shortest path is not unique, you are supposed to output the one with the minimum cost, which is guaranteed to be unique.

### Input Specification:

Each input file contains one test case. Each case starts with a line containing 4 positive integers *N*, *M*, *S*, and *D*, where *N* (≤500) is the number of cities (and hence the cities are numbered from 0 to *N*−1); *M* is the number of highways; *S* and *D* are the starting and the destination cities, respectively. Then *M* lines follow, each provides the information of a highway, in the format:

```
City1 City2 Distance Cost
```

where the numbers are all integers no more than 500, and are separated by a space.

### Output Specification:

For each test case, print in one line the cities along the shortest path from the starting point to the destination, followed by the total distance and the total cost of the path. The numbers must be separated by a space and there must be no extra space at the end of output.

### Sample Input:

```in
4 5 0 3
0 1 1 20
1 3 2 30
0 3 4 10
0 2 2 20
2 3 1 20
```

### Sample Output:

```out
0 2 3 3 40
```

## 解题思路

找最短径，所以用**Dijkstra算法**，由于题目中还给了第二个路径选择条件，就是路径总花销，所以需要做一点改动，就是在每次更新最短径的时候，多做一点判断，判断当前节点的总花销是否也更低，如果更低那么这个节点的就放到花销更少的路径上。

然后，写一下Dijkstra算法的大致代码：

```c
void Dijkstra(){
    while(1){
        V = 为收录顶点中dist最小者;// 也就是collected中值为false的点中具有最小距离的点
        if(这样的V不存在) break;//也就是遍历完了所有的节点
        collected[V] = true; // 表示这个点已经被遍历过了
        for( V的每一个邻接点W ){
            // 下面的判断可能没有太大需要，因为一个点如果被遍历了，那么它存留的最短路径在当前来说一定是会小于dist[V]+E的，因为W如果被遍历过，那么W就是在V之前被遍历，其最短径长度必然小于V，更不用说还要加上一个E了，所以if(dist[V]+E<v,w> < dist[W])完全足够。
            if(collected[W] == false){
                if(dist[V]+E<v,w> < dist[W]){// 如果邻接点通过V到达原点的距离更小，那么就更新现在的邻接点到原点的距离值
                    dist[W] = dist[V] + E<v,w>;
                    path[W] = V;// path中如此存储表示在路径上W的前一个节点是V，最后通过写个递归函数很容易可以打印出当前路径
                }
            }
        }
    }
}
```

## 代码

```c
#include <stdio.h>
#include <vector>
using namespace std;

typedef struct{
    int CityNum;
    int Cost;
    int Distance;
} APCITY;
typedef struct{
    //apcity 与当前城市直接相连的城市。包含距离，花销，城市编号
    vector<APCITY> apcity;
    //当前城市是否被遍历
    bool traverse;
    //到当前城市的最短距离
    int shortestdist;
    //到当前城市的总花销
    int totalcost;
} CITY;

int Path[501], leftCityNum, N;
CITY citys[501];

// 找 原点到还没遍历的点之间的距离 最短的点
int findShortestDistCity(){
    int min = 1000, index = 0;
    for(int i = 0; i < N; i++){
        if(!citys[i].traverse && citys[i].shortestdist < min){
            min = citys[i].shortestdist;
            index = i;
        }
    }
    return index;
}

void Dijkstra() {
    int V, APsize, W;
    // leftCityNum表明还没有被赋值给最短距离属性的City个数
    for(int i = leftCityNum; i > 0; i--){
        V = findShortestDistCity();
        citys[V].traverse = true;
        APsize = (int)citys[V].apcity.size();
        for(int j = 0; j < APsize; j++){
                W = citys[V].apcity[j].CityNum;
            	// 当前点到原点的距离+当前点到邻接点W的距离 < W之前存储的到原点的距离
                if(citys[V].shortestdist + citys[V].apcity[j].Distance < citys[W].shortestdist){
                    citys[W].shortestdist = citys[V].shortestdist + citys[V].apcity[j].Distance;
                    citys[W].totalcost = citys[V].totalcost + citys[V].apcity[j].Cost;
                    Path[W] = V;
                    // 当前点到原点的距离+当前点到邻接点W的距离 == W之前存储的到原点的距离 &&
                    // 通过当前点到邻接点W的总花销如果比W之前那条路径的总花销小，那么就可以替换W所在的路径，也就是修改W的前一个节点为当前节点V
                }else if(citys[V].shortestdist + citys[V].apcity[j].Distance == citys[W].shortestdist && citys[V].totalcost + citys[V].apcity[j].Cost < citys[W].totalcost){
                    citys[W].totalcost = citys[V].totalcost + citys[V].apcity[j].Cost;
                    Path[W] = V;
                }
            }
    }
}
//路径打印函数
void printPath(int V){
    if(V == -1) return;
    printPath(Path[V]);
    printf("%d ", V);
}

int main() {
    int M, S, D;
    scanf("%d %d %d %d", &N, &M, &S, &D);
    int City1, City2, Distance, Cost;
    APCITY info;
    for(int i =0; i < N; i++){
        citys[i].shortestdist = 1000;
        citys[i].traverse = false;
        citys[i].totalcost = 0;
        Path[i] = -1;
    }
    for(int i = 0; i < M; i++){
        scanf("%d %d %d %d", &City1, &City2, &Distance, &Cost);
        info.CityNum = City2;
        info.Distance = Distance;
        info.Cost = Cost;
        citys[City1].apcity.push_back(info);
        info.CityNum = City1;
        citys[City2].apcity.push_back(info);
    }
    
    citys[S].shortestdist = 0;
    leftCityNum = N - 1;
    
    Dijkstra();
    
    printPath(D);
    printf("%d %d\n", citys[D].shortestdist, citys[D].totalcost);
    return 0;
}

```

