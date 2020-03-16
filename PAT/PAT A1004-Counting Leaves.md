# PAT A1004-Counting Leaves

## 题目

A family hierarchy is usually presented by a pedigree tree. Your job is to count those family members who have no child.

### Input Specification:

Each input file contains one test case. Each case starts with a line containing 0<*N*<100, the number of nodes in a tree, and *M* (<*N*), the number of non-leaf nodes. Then *M* lines follow, each in the format:

```
ID K ID[1] ID[2] ... ID[K]


      
    
```

where `ID` is a two-digit number representing a given non-leaf node, `K` is the number of its children, followed by a sequence of two-digit `ID`'s of its children. For the sake of simplicity, let us fix the root ID to be `01`.

The input ends with *N* being 0. That case must NOT be processed.

### Output Specification:

For each test case, you are supposed to count those family members who have no child **for every seniority level** starting from the root. The numbers must be printed in a line, separated by a space, and there must be no extra space at the end of each line.

The sample case represents a tree with only 2 nodes, where `01` is the root and `02` is its only child. Hence on the root `01` level, there is `0` leaf node; and on the next level, there is `1` leaf node. Then we should output `0 1` in a line.

### Sample Input:

```in
2 1
01 1 02
```

### Sample Output:

```out
0 1
```

## 解题思路

广度优先遍历，同时标注节点所在层次，对应记录对应层的叶子节点。

## 代码

```c
#include <stdio.h>
#include <queue>

using namespace std;

typedef struct{
    int K;
    int level;
    vector<int> Child;
} NODE;

queue<int> Q;// 队列 用于广度优先遍历
int cnt[110], level = 0;
void BFS(NODE *node) {
    int V = 1;// 初始化第一个节点1 入队
    node[V].level = 0;// 当前是第一层所以标注当前节点的层次为0
    Q.push(V);
    while(!Q.empty()){// 队列为空时所有节点也就遍历完了
        V = Q.front();// 第一个节点出队列表示第一个节点正在被遍历
        Q.pop();
        // 如果当前节点没有儿子节点那么它所在的那层就增加一个leaf的个数
        if(node[V].K == 0) cnt[node[V].level]++;
        for(int i = 0; i < node[V].K; i++){
            // 儿子节点的层次比父亲节点多1
            node[node[V].Child[i]].level = node[V].level + 1;
            Q.push(node[V].Child[i]);//儿子节点入队，为儿子节点出队被遍历做准备
        }
    }
    level = node[V].level;
}

int main () {
    int N, M;
    scanf("%d", &N);
    if(N == 0) return 0;
    scanf("%d", &M);
    NODE node[N+1];
    // node不是全局变量所以不会被默认初始化，所以一定要初始化赋初值
    for(int i = 0; i < N+1; i++){
        node[i].K = 0;
    }
    int ID, K, ChildID;
    for(int i = 0; i < M; i++){
        scanf("%d %d", &ID, &K);
        node[ID].K = K;
        for(int j = 0; j < K; j++){
            scanf("%d", &ChildID);
            node[ID].Child.push_back(ChildID);
        }
    }
    
    BFS(node);
    printf("%d", cnt[0]);
    for(int i = 1; i <= level; i++){
        printf(" %d", cnt[i]);
    }
    printf("\n");
    return 0;
}

```

