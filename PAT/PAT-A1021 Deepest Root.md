# PAT-A1021 Deepest Root

## 题目

A graph which is connected and acyclic can be considered a tree. The height of the tree depends on the selected root. Now you are supposed to find the root that results in a highest tree. Such a root is called **the deepest root**.

### Input Specification:

Each input file contains one test case. For each case, the first line contains a positive integer *N* (≤104) which is the number of nodes, and hence the nodes are numbered from 1 to *N*. Then *N*−1 lines follow, each describes an edge by given the two adjacent nodes' numbers.

### Output Specification:

For each test case, print each of the deepest roots in a line. If such a root is not unique, print them in increasing order of their numbers. In case that the given graph is not a tree, print `Error: K components` where `K` is the number of connected components in the graph.

### Sample Input 1:

```in
5
1 2
1 3
1 4
2 5
```

### Sample Output 1:

```out
3
4
5
```

### Sample Input 2:

```in
5
1 3
1 4
2 5
3 4
```

### Sample Output 2:

```out
Error: 2 components
```

## 解题思路

这个题可以看作是要完成两个任务，一个是找连通图个数，一个是找最深根。

对于第一个任务，可以通过DFS深度优先遍历来做，思路就是DFS可以判断这么多个点是否联通，多次DFS直到遍历完所有提供的点那么就找完了所有的连通图个数。
还可以通过**并查集**进行查找，这段代码就是用的并查集的方式，并查集可以根据提供的关系发现两个节点是否属于一个集合。

之后第二个任务，就通过深度优先遍历来做，第一遍深度优先遍历任选一个节点作为初始点，然后找到在这个节点作为根节点时离它最远的节点，再从这些节点中任选一个作为根节点进行第二次深度优先遍历，找到最远的节点，两次遍历得到的节点集合做并集得到的就是最深根的集合。

之后输出结果即可。

其实对于上面两次遍历取并，可能有点难理解，那么做些解释。

首先，题目所给的树必然存在一条最长的路径，我们想象把这两个端点拉住将这条路径拉直，那么就成了下图中红色的这一条线，红线上也有需许许多多的节点。那么其他节点就可以像下图绿线这样画出来。如果把红线上出现分支的点看成一个分割点，把红线成左右两部分，那么有一点可以确定分支的长度必然不会大于红线左部分或者红线右部分的长度。而这样的一个节点表明的是什么，表明的是在这个节点路径的发展有多个选择，你可以选择红线或是选择分支绿线，但是由分支长度不会大于红线做部分或右部分长度可知，出现这种可选支路节点的时候选择红线才能保证路径最长，那么比如现在从图上的黑点出发，我必然会到达红线，之后只有在任意分叉口都选择红线才能得到一条最长路径，所以到这里可以知道，第一次的深度优先遍历必然会找到红线的一端，第二次是以红线的一端为根节点开始深度优先遍历，所以在最长路径的端点必然在红线的另一端，那么就找到了最终要找的节点了。

<img src="/Users/liyongyang/Documents/考研/PAT/img/img_1.png" alt="img_1" style="zoom:50%;" />

由于最长的路径它不一定只有一条而第一次和第二次遍历的结果都是红线上的端点，他们都可以作为`the deepest root`所以最后取并得到最终结果。

## 代码

```c
#include <stdio.h>
#include <vector>
#include <map>

using namespace std;

typedef struct {
    bool Flag;
    vector<int> AdjacentPoint;
}NODE;

int UFset[10010], N, K;
NODE T[10010];
vector<int> deepestroot;
map<int, int> mp;
int maxd = 0;

/*-------------------并查集--------------------*/
void init() {
    for(int i = 1; i <= N; i++){
        UFset[i] = i;
    }
}
int Find(int x){
    if(UFset[x] == x) return x;
    return UFset[x] = Find(UFset[x]);
}
void Unite(int x1, int x2){
    int p1 = Find(x1);
    int p2 = Find(x2);
    if(p1 != p2){
        UFset[p2] = p1;
        K--;// K是一个全局变量，K=N，N就是N个点，每Unite一次，两个点代表的两个集合合为1个集合，所以K--。
    }
}
/*-------------------并查集--------------------*/
/*-------------------DFS--------------------*/
void DFS(int V, int d){
    T[V].Flag = true;
    d++;
    int APsize = (int)T[V].AdjacentPoint.size();
    int AP;
    if(d > maxd){
      //如果当前的深度比之前记录的最大深度还要大，那么之前认定的最远节点就不成立，于是清除之前的数据现在进行更新。
        deepestroot.clear();
        deepestroot.push_back(V);
        maxd = d;
    }else if(d == maxd){// 最远节点不止一个所以遇到等同长度的也要存下来
        deepestroot.push_back(V);
    }
    for(int i = 0; i < APsize; i++){
        AP = T[V].AdjacentPoint[i];
        if(!T[AP].Flag) DFS(AP, d);
    }
}
/*-------------------DFS--------------------*/

bool cmp(int a, int b){
    return a < b;
}
int main() {
    scanf("%d", &N);
    int x1, x2;
    init();
    K = N;
    for(int i = 0; i < N-1; i++){
        scanf("%d %d", &x1, &x2);
        Unite(x1, x2);
        T[x1].AdjacentPoint.push_back(x2);
        T[x2].AdjacentPoint.push_back(x1);
        T[i+1].Flag = false;
    }
    if(K != 1) printf("Error: %d components\n", K);
    else {
        maxd = 0;
        DFS(1, 0);
        for(int i = 1; i <= N; i++) T[i].Flag = false;
        for(vector<int>::iterator it = deepestroot.begin(); it != deepestroot.end(); it++){
            mp[*it] = 1;
        }
        deepestroot.clear();

        DFS(mp.begin()->first, 0);
        for(vector<int>::iterator it = deepestroot.begin(); it != deepestroot.end(); it++){
            mp[*it] = 1;
        }
        for(map<int, int>::iterator it = mp.begin(); it != mp.end(); it++){
            printf("%d\n", it->first);
        }
    }
    return 0;
}

```

