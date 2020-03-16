# PAT A1013-Battle Over Cities

## 题目

It is vitally important to have all the cities connected by highways in a war. If a city is occupied by the enemy, all the highways from/toward that city are closed. We must know immediately if we need to repair any other highways to keep the rest of the cities connected. Given the map of cities which have all the remaining highways marked, you are supposed to tell the number of highways need to be repaired, quickly.

For example, if we have 3 cities and 2 highways connecting *c**i**t**y*1-*c**i**t**y*2 and *c**i**t**y*1-*c**i**t**y*3. Then if *c**i**t**y*1 is occupied by the enemy, we must have 1 highway repaired, that is the highway *c**i**t**y*2-*c**i**t**y*3.

### Input Specification:

Each input file contains one test case. Each case starts with a line containing 3 numbers *N* (<1000), *M* and *K*, which are the total number of cities, the number of remaining highways, and the number of cities to be checked, respectively. Then *M* lines follow, each describes a highway by 2 integers, which are the numbers of the cities the highway connects. The cities are numbered from 1 to *N*. Finally there is a line containing *K* numbers, which represent the cities we concern.

### Output Specification:

For each of the *K* cities, output in a line the number of highways need to be repaired if that city is lost.

### Sample Input:

```in
3 2 3
1 2
1 3
1 2 3
```

### Sample Output:

```out
1
0
0
```

## 解题思路

题目要求我们做的就是除开指定的点判断连通图个数，用并查集或者DFS都可以做，只是DFS要多次调用，可能有些麻烦，这里用并查集解决。

## 代码

```c
#include <stdio.h>

using namespace std;

int P[1010];
typedef struct{
    int a;
    int b;
} ADJACENT;
int LeftCityNum;
/*----------并查集----------*/
void init(){
    for(int i = 0; i < 1010; i++){
        P[i] = i;
    }
}
int Find(int x){
    if(x == P[x]) return x;
    return P[x] = Find(P[x]);
}
void Unite(int x1, int x2){
    int P1 = Find(x1);
    int P2 = Find(x2);
    if(P[P1]!=P[P2]){
        P[P1] = P2;
        LeftCityNum--;
    }
}
/*----------并查集----------*/
int main() {
    int N, M, K;
    scanf("%d %d %d", &N, &M, &K);
    ADJACENT AP[M];
    for(int i = 0; i < M; i++){
        scanf("%d %d", &AP[i].a, &AP[i].b);
    }
    int CheckPoint[K];
    for(int i = 0; i < K; i++){
        scanf("%d", &CheckPoint[i]);
    }
    for(int i = 0; i < K; i++){
        init();
        LeftCityNum = N - 1;
        for(int j = 0; j < M; j++){
            if(AP[j].a != CheckPoint[i] && AP[j].b != CheckPoint[i]){
                Unite(AP[j].a, AP[j].b);
            }
        }
        printf("%d\n", LeftCityNum-1);
    }
    return 0;

}
```

