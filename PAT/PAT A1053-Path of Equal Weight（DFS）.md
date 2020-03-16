# PAT A1053-Path of Equal Weight（DFS）

## 题目

Given a non-empty tree with root *R*, and with weight *W**i* assigned to each tree node *T**i*. The **weight of a path from \*R\* to \*L\*** is defined to be the sum of the weights of all the nodes along the path from *R* to any leaf node *L*.

Now given any weighted tree, you are supposed to find all the paths with their weights equal to a given number. For example, let's consider the tree showed in the following figure: for each node, the upper number is the node ID which is a two-digit number, and the lower number is the weight of that node. Suppose that the given number is 24, then there exists 4 different paths which have the same given weight: {10 5 2 7}, {10 4 10}, {10 3 3 6 2} and {10 3 3 6 2}, which correspond to the red edges in the figure.

![img](https://images.ptausercontent.com/212)

### Input Specification:

Each input file contains one test case. Each case starts with a line containing 0<*N*≤100, the number of nodes in a tree, *M* (<*N*), the number of non-leaf nodes, and 0<*S*<230, the given weight number. The next line contains *N* positive numbers where *$W_i$* (<1000) corresponds to the tree node *$T_i$*. Then *M* lines follow, each in the format:

```
ID K ID[1] ID[2] ... ID[K]
```

where `ID` is a two-digit number representing a given non-leaf node, `K` is the number of its children, followed by a sequence of two-digit `ID`'s of its children. For the sake of simplicity, let us fix the root ID to be `00`.

### Output Specification:

For each test case, print all the paths with weight S in **non-increasing** order. Each path occupies a line with printed weights from the root to the leaf in order. All the numbers must be separated by a space with no extra space at the end of the line.

Note: sequence {*$A_1$*,*$A_2$*,⋯,*$A_n$*} is said to be **greater than** sequence {*$B_1$*,*$B_2$*,⋯,*$B_m$*} if there exists 1≤*k*<*m**i**n*{*n*,*m*} such that *$A_i$*=*$B_i$* for *i*=1,⋯,*k*, and *$A_{k+1}$*>*$B_{k+1}$*.

### Sample Input:

```in
20 9 24
10 2 4 3 5 10 2 18 9 7 2 2 1 3 12 1 8 6 2 2
00 4 01 02 03 04
02 1 05
04 2 06 07
03 3 11 12 13
06 1 09
07 2 08 10
16 1 15
13 3 14 16 17
17 2 18 19
```

### Sample Output:

```out
10 5 2 7
10 4 10
10 3 3 6 2
10 3 3 6 2
```

## 解题思路

由于是要求一整条路径，于是很自然能想到`DFS`，要用深度优先，就两个很重要的点：1、标记遍历，2、遍历邻接点。题目直接给出了具有邻接点的节点的所有邻接点。开始时，从00节点开始遍历，然后遍历其第一个邻接点，在遍历第一个邻接点时，会递归调用DFS，于是又会递归遍历到第一个邻接点的第一个邻接点，如此往复遍历完所有节点。由于这种遍历的特性，我们需要先遍历那些权值比较大的邻接点，因为题目要求最先出现大权值的路径要被先打出来，所以先遍历较大权值的邻接点可以较先遍历到符合条件的大权值尽早出现的路径，所以在读取数据后，把每个点的邻接点按照权值由大到小进行排列可以使得之后遍历时每个节点总是先遍历权值较大的点，当我们找到了一条符合条件的路径就要把它打印出来，于是使用节点里的`Father`字段，递归找到，路径的所有节点，然后对这些节点进行打印就得到最终结果。

## 代码

```c
#include <stdio.h>
#include <algorithm>
#include <vector>

using namespace std;

struct node{
    int Weight; // 点的权值
    vector<int> AdjacentPoint; // 每个点的邻接点，也就是它的儿子节点
    int Father;// 节点的父亲节点
    bool Flag;// 是否被遍历过
};
typedef struct node NODE;

unsigned int S;
NODE T[110];

bool cmp(int a, int b){
    return T[a].Weight > T[b].Weight;// 根据权值为邻接点排序
}
// 递归找寻父亲节点，把路径权值打印出来
void printPath(int V){
    if(T[V].Father != -1) {
        printPath(T[V].Father);
        printf(" %d", T[V].Weight);
    }else {
        printf("%d", T[V].Weight);
    }
}

// DFS当遍历点V时，首先把其Flag改为true说明已经被遍历，接着再遍历V的所有邻接节点
void DFS(int V, int sum){
    T[V].Flag = true; // 标记说明该点已经遍历过
    sum += T[V].Weight;// 加上当前节点该路径目前的权值
    if(sum == S && T[V].AdjacentPoint.size() == 0) {// 当某个节点处当前权值为目标值，并且它没有邻接点的时候就得到了一条完整路径，将它打印出来
        printPath(V);
        printf("\n");
    }
    int APsize = (int)T[V].AdjacentPoint.size();
    for(int i = 0; i < APsize; i++){// 遍历所有邻接节点
        DFS(T[V].AdjacentPoint[i], sum);
    }
}
int main() {
    int N, M;
    scanf("%d %d %d", &N, &M, &S);
    
    for(int i = 0; i < N; i++){
        scanf("%d", &T[i].Weight);
        T[i].Flag = false;
    }
    T[0].Father = -1;
    int ID, K, AP;
    for(int i = 0; i < M; i++){
        scanf("%d %d", &ID, &K);
        for(int j = 0; j < K; j++){
            scanf("%d", &AP);
            T[ID].AdjacentPoint.push_back(AP);
            T[AP].Father = ID; // 当前AP指的是ID的所有邻接点，那么所有AP的父亲节点为当前ID
        }
        // 以权值对邻接点进行排序，因为之后的DFS遍历是依据这个AdjacentPoint的存放顺序来遍历的，所以它会先遍历AdjcentPoint中靠前的点，那么排序后，权值更大的就排在更前面，也就更早被遍历到，于是由此可以保证最先打印出来的一定是更早出现大权值的路径。
        sort(T[ID].AdjacentPoint.begin(), T[ID].AdjacentPoint.end(), cmp);
    }
    DFS(0, 0);// 初始值为：起始点00，其实总和sum = 0
    
    return 0;
}

```

